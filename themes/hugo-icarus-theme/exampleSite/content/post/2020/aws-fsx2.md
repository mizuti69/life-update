+++
title = "AWS FSx と名前空間"
tags = ["aws"]
categories = ["SE"]
date = "2020-10-20"
description = "AWS FSx を利用する上で考慮しなければいけ無いこと"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

<blockquote class="embedly-card" data-card-controls="0"><h4><a href="https://aws.amazon.com/jp/fsx/">Amazon FSx | マネージド型共有ファイルストレージシステム | アマゾン ウェブ サービス</a></h4></blockquote>
<script async src="//cdn.embedly.com/widgets/platform.js" charset="UTF-8"></script>  

## 可用性と耐久性  
<i class="fas fa-external-link-alt"></i> [可用性と耐久性: シングル-AZ および Multi-AZ ファイル・システム](https://docs.aws.amazon.com/ja_jp/fsx/latest/WindowsGuide/high-availability-multiAZ.html#set-up-fsx-dfs)  

FSx を採用する上で考えなければいけないことは以下のようなことがあり  

* FSxには64TB容量の上限がある
* シングル、マルチAZ構成  
* バックアップから復旧する際は EC2 等同様に新規インスタンスで立ち上がる  

冗長性はもちろん、拡張性も考慮しなければいけない  
バックアップから復旧するときも、DNS名、IPアドレスが変わるためクライアント側の対応も考慮しなければいけない  

AWSの公式マニュアルにもあるとおり、現状では配置リージョンによって取れる手法が異なっている  

<img src="/images/2020/aws/fsx/dfs-01.png" />  

ちなみに色々セットアップ完了後に知ってちょっと絶望した  
幸い各方式においても DFS名前空間はサポートされているため、DFSを設定してみる  

## AWS FSx DFS名前空間の設定
[DFSネームスペースを使用した複数のファイル システムのグループ化](https://docs.aws.amazon.com/ja_jp/fsx/latest/WindowsGuide/group-file-systems.html)  

### DFS名前空間のインストール  
ドキュメントでは CloudFormation から Windows Server の EC2 デプロイからとなっているがすでに AD 管理用にあるためそちらを利用する  

機能の追加から「DFS Namespaces」を選択してインストール  

<img src="/images/2020/aws/fsx/dfs-02.png" />  

機能の追加に伴う再起動は不要  

### DFS名前空間の設定
DFSサーバを指定し、起点とする共有フォルダを作成する  
DFSは AWS DS Managed MicrosoftAD クライアントとして立てた Windowsサーバーを利用する  

管理ツールから DFS管理 を起動し「新しい名前空間」を作成します  

<img src="/images/2020/aws/fsx/dfs-03.png" />  
<img src="/images/2020/aws/fsx/dfs-04.png" />  

DFSサーバにするサーバを指定、この場合はローカルサーバのコンピュータ名を入力  

<img src="/images/2020/aws/fsx/dfs-05.png" />  

DFSにより管理する共有領域の起点となるパス、フォルダー名を指定  

<img src="/images/2020/aws/fsx/dfs-06.png" />  

名前入空間の種類、ここはそのまま  

<img src="/images/2020/aws/fsx/dfs-07.png" />  

問題なければ内容を確認し作成する  

### DFSメンバーの登録  
DFSをインストールし、DFSサーバを設定し、
DFSサーバ上に起点となる共有フォルダ領域が出来たので、その配下にメンバーとなるFSxを登録する  

DFS管理ツールより作成した名前空間に新しいフォルダー作成を選択し  

<img src="/images/2020/aws/fsx/dfs-08.png" />  

フォルダーターゲットにFSxを指定する  

<img src="/images/2020/aws/fsx/dfs-09.png" />  

作成すると起点の共有フォルダー内にDFS管理のショートカットが作成され  
クライアントから対象フォルダのパスにアクセスすると FSx の領域にリダイレクトされる  

> \\＜domain name＞\＜folder name＞\＜DFS link for FSx＞

AWS DS を利用している場合、設定内容が反映されるまで少し時間がかかる  

---

FSxにて大容量のサイズ運用（64TB以上）や複数FSxインスタンス運用をする可能性がある場合に DFS という選択肢からは逃げられない  
しかし DFS にも注意点があり  

* DFSサーバが起点のファイルサーバになるため常に起動しておく必要がある  
* DFSサーバが停止した場合は共有領域にアクセスできなくなるため、DFSサーバの冗長化の検討が必要  
* FSxと連携する場合、DFS側起点フォルダ権限管理とFSx側のフォルダ権限管理などちょっと考慮が必要  

と、せっかくクラウドでファイルサーバ！ってやってるのに  
やってることはオンプレ的な運用とそう大きくは変われない。ストレージサーバの面倒を見なくて良くなったくらいだね  
FSxもマルチAZに対応したり、ストレージサイズ、スループットのオンデマンド変更できるようになったりと  
サービス機能もユーザーの要望に沿ってきてるけど、やはりまだまだ足りない感はあるし、
AWS の感じ的にエンタープライズ向けにそんな力入れてる感じも無いし、Azure や Office365 がやっぱり良いと思った  

大規模に「クラウド移行！」というより、ミニマムに使い始めるのが多分丁度いい  

{{< rawhtml >}}
<div style="text-align: center;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=13&l=ur1&category=literaturebooks&banner=00Q2AEJJKVRA5ZR7ZY02&f=ifr&linkID=3491ab0808419ea4886363dcfa4c8be7&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="468" height="60" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
