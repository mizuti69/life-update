+++
title = "AWS DS Managed MicrosoftAD と FSx 構築"
tags = ["aws"]
categories = ["SE"]
date = "2020-10-15"
description = "AWS DS Managed MicrosoftAD + ClientVPN 環境に FSx も追加する"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

<blockquote class="embedly-card" data-card-controls="0"><h4><a href="https://aws.amazon.com/jp/fsx/">Amazon FSx | マネージド型共有ファイルストレージシステム | アマゾン ウェブ サービス</a></h4></blockquote>
<script async src="//cdn.embedly.com/widgets/platform.js" charset="UTF-8"></script>  

## FSx
FSxには2つのサービスがある  

**●Amazon FSx for Windows ファイルサーバー**  
Amazon FSx for Windows ファイルサーバーは、業界標準のサーバーメッセージブロック (SMB) プロトコルを介してアクセスできる、完全マネージド型のファイルストレージを提供  

**●Amazon FSx for Lustre**  
Amazon FSx for Lustre は、コスト効率が高い、高性能ストレージをコンピューティングワークロード向けに提供する、フルマネージド型サービスです。機械学習、高性能コンピューティング (HPC)、ビデオレンダリング、金融シミュレーションといった多くのワークロードは、高性能共有ストレージを介して同じデータセットにアクセスするコンピューティングインスタンスに依存しています。世界で最も人気のある高性能ファイルシステムである Lustre を使用した FSx for Lustre では、低レイテンシーの共有ストレージ、1 秒間に最大で数百ギガバイトのスループット、百万単位の IOPS を提供  


高性能、高レイテンシを求める場合で Windows 的な制約が無いのであれば Luster という選択肢もある  
今回は通常オフィスワーク利用を想定しているので for Windows を採用する  

## Amazon Directory Service Managed MicrosoftAD の準備
すでに準備できているため割愛  
[>> AWS ClientVPN + AD相互認証① ](/2020/03/30/aws-clientvpn/)  
[>> AWS ClientVPN + AD相互認証② ](/2020/04/07/aws-clientvpn2/)  

## FSx構築
AWS DS Managed MicrosoftAD と AWS Client VPN 環境はすでに出来ているため  
パブリックではなく、Trustネットワーク上に FSx を構築し、VPN経由でのみアクセス出来るようにする  

### FSxの作成

<img src="/images/2020/aws/fsx/win-01.png" />  
<img src="/images/2020/aws/fsx/win-02.png" />  

Windows File Server を選択  

<img src="/images/2020/aws/fsx/win-03.png" />  

● File system name  
任意  

● Deployment type  
FSxに求める冗長性で考慮する、マルチAZを選択した場合は当然料金は２倍になる  
**後で変更は不可、バックアップから復旧する際に変更することは出来る**  

● Strage type  
採用するストレージタイプを選択  
SSD、HDDどちらにするかで課金体系が変わる HDDのほうが安い  
AWS内での参照ではなく、外部ネットワークから FSx を利用する場合、ストレージ性能の前にネットワークがボトルネックになりそうなのでここでは HDD  
利用要件に合わせて選択する  
**後で変更は不可、バックアップから復旧する際に変更することは出来る**  

● Strage capacity  
利用ストレージサイズ、最小でも 2TB以上、最大でも65TBにする必要がある  
**後から変更可能**  

● Specify thoroughput capacity  
ストレージ等要求スループット速度  
ストレージタイプとスループットで料金テーブルが変わる、スループットを高くすると結構高い  
**後から変更可能**なため、利用状況を見て適切な値に変更する運用をしたほうが良さそう  
ストレージ追加時には最低要求スループットがあるため、最低値で運用する場合は注意が必要  

<img src="/images/2020/aws/fsx/win-04.png" />  

● Network & security  
配置する VPC、サブネット、セキュリティグループを指定する  
FSx、AWS DS Managed MicrosoftAD を連携して利用する場合は下記通信要件を確認して必要な通信が出来るようなセキュリティグループを設定する  

<i class="fas fa-external-link-alt"></i> [Amazon FSx を Microsoft Active Directory (Enterprise Edition) 用の AWS Directory Service に使用する](https://docs.aws.amazon.com/ja_jp/fsx/latest/WindowsGuide/fsx-aws-managed-ad.html)  

● Windows authenication  
事前にある AWS DS を指定  

●  Encryption
特に持ち込みは無いためデフォルトで  

<img src="/images/2020/aws/fsx/win-05.png" />  

●バックアップ設定  
日次でのバックアップ保持数とメンテナンスウィンドウを指定できる  
**後で変更可能**  

●メンテナンスウィンドウの設定
恐らく Windows Server なのでパッチ適応等が週次で発生するらしい  
そのためのメンテナンスウィンドウ指定  
特に停止とかはしないようですが、シングル構成の場合は３０分前後利用できないくらいの気持ちで居たほうが良さそう  
**後で変更可能**  

<i class="fas fa-external-link-alt"></i> [Amazon FSx はどのようにして Windows Server ソフトウェアを最新の状態に保ちますか?](https://aws.amazon.com/jp/fsx/windows/faqs/)  


確認画面で問題なければ作成する  
作成には数十分かかる場合もあるため注意  

### 利用方法  
FSxが出来、ADと連携できていれば後はオンプレでファイルサーバを利用するのと同様にアクセスすることが出来る  
FSxには「DNS名」と「IP」が自動で割り振られるので指定してアクセスする  

<img src="/images/2020/aws/fsx/win-06.png" />  

VPN経由でのみ許可している環境なので  
VPNつないだ後 DNS 名を名前解決出来ることを確認し接続する  
※ALBのように内部LBでもパブリックDNSで名前解決できるとかは無く、ADのドメイン名でADをDNS参照した場合に名前解決出来るようになっている  
※AD、FSxのドメイン名、利用するクライアントPCがどのDNSを参照する設定にしているかをちゃんと考えておく  
※AD認証しているため、IPを違うパブリックなドメイン等でDNS登録してもアクセス出来ない  

<img src="/images/2020/aws/fsx/win-07.png" width="70%"/>  
<img src="/images/2020/aws/fsx/win-08.png" width="70%"/>  
<img src="/images/2020/aws/fsx/win-09.png" width="70%"/>  

FSxのマウントポイントは最後に「share」をつける  

> ￥￥＜FSx dns name or ip＞￥share

<img src="/images/2020/aws/fsx/win-10.png" width="70%"/>  

通信が上手く行けば AD の認証画面が出てくる  
数分時間がかかる場合があった  


### FSx の権限
フォルダ等作成してみると、デフォルトで下記のように権限付けされている  
<img src="/images/2020/aws/fsx/win-11.png"/>  

「Authenicated Users」が AD で認証された人  
「AWS Delegrated FSx Administorators」 が AWS 管理のグループ  

フォルダ等権限の継承も有効化されおり、「Authenicated Users」でも基本的な作成、削除、権限変更が出来てしまうため  
なんならマウントポイントの「share」フォルダの権限変更も出来てしまい、FSxが全く利用できない状況にされてしまうこともある  
大人数で使う場合は最初に「share」フォルダの権限を修正し、  
管理者権限がほしいユーザーは「AWS Delegrated FSx Administorators」に追加するか  
専用のグループを作って権限付けする運用をしたほうが良い  


## 注意点  
●バックアップについて  
バックアップからの復旧は EC2 や RDS 同様コピー環境をデプロイする形になります  
なのでよほどのことが無い限り、ファイル単位での復旧等は現実的ではない  

●ウイルス対策等は無し  
この辺は運用でカバーできないので注意  

●クオータ設定  
ない。ユーザー単位のクオーターはある  

●各種ログ  
ない  

という便利だけど痛いところに手が届かない  
エンタープライズに利用しようとするとちょっと現状だと弱い印象  
素直に Microsoft Office360 とか検討しましょう  

{{< rawhtml >}}
<div style="text-align: center;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=13&l=ur1&category=literaturebooks&banner=00Q2AEJJKVRA5ZR7ZY02&f=ifr&linkID=3491ab0808419ea4886363dcfa4c8be7&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="468" height="60" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
