+++
title = "AWS Transfer for SFTP 検証"
tags = ["aws"]
categories = ["SE"]
date = "2019-07-25"
description = "AWS Transfer for SFTP を検証して実務で利用できそうなものなのか確認してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## 概要  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/sftp/" data-iframely-url="//cdn.iframe.ly/oZGA16T?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

Amazon S3 と　SFTP プロトコルで直接ファイルのやり取りができるマネージドサービス  

### 料金
<i class="fas fa-external-link-alt"></i> [AWS Transfer for SFTP の料金](https://aws.amazon.com/jp/sftp/pricing/)  
転送量 + エンドポイントのプロビジョニング時間あたりでも金額が発生する  

## セットアップ
<i class="fas fa-external-link-alt"></i> [SFTP サーバーの作成](https://docs.aws.amazon.com/ja_jp/transfer/latest/userguide/getting-started-server.html)  

### SFTPサーバの作成  
エンドポイントタイプの選択  
**Public** の場合インターネット経由
**VPC** の場合は VPC内のサーバからあkすえすする場合  
**Custom hostname** を指定すると任意のドメインを割り振ることも出来る  

<img src="/images/2019/aws-sftp/sftp-setup01.png" />  

認証方法の選択  
**Servic managed** の場合は AWS SFTP に ユーザー情報を保存する  
**Custom** の場合ンドポイントにアクセスするために API Gateway エンドポイントと IAM ロールが提供される  

<img src="/images/2019/aws-sftp/sftp-setup02.png" />  

ロギング  
他のAWSサービスからアクティビティログに対するアクセスを追加出来るようロールを指定することも出来る  

<img src="/images/2019/aws-sftp/sftp-setup03.png" />  

作成  

<img src="/images/2019/aws-sftp/sftp-setup04.png" />  

### ユーザーの追加
「Add User」から SFTP を使えるユーザーを登録する  
以下のような制約があるため、環境やユーザーによっては複数 SFTP エンドポイントを用意する必要がありそう  

> サービスマネージド型 ID タイプを使用する場合、SFTP サーバーにユーザーを追加します。その場合、各ユーザー名はサーバーで一意である必要があります。

<img src="/images/2019/aws-sftp/sftp-setup05.png" />  

**Username** IAMユーザー名かと思ったけどそうじゃない？  
**Access** ログインしたい S3 に対してアクセス権を持つ Role を選択する  
**Policy**  [SFTP のための IAM ポリシーおよびロールの作成](https://docs.aws.amazon.com/ja_jp/transfer/latest/userguide/requirements-roles.html) にて詳細に権限等を設定できる  
**Home directory** ログインする S3 バケットとオブジェクトパスを指定  

> ユーザーのユーザー名が含まれるディレクトリパスを選択することをお勧めします。そうすることで、スコープダウンポリシーを効果的に使用できます。スコープダウンポリシーは、S3 バケットでのユーザーアクセスを home ディレクトリに制限します。  

ポリシー等によりアクセスを細かく制限する場合はログインディレクトリも指定したほうがいいよと、ここは OS の考え方と同じ  
たとえ制限しなくても ユーザー名のパスにしたほうがいいか  

SSH 秘密鍵、公開鍵の作成と登録  
クライアント側の Windows10 WinSCP にてキーペアを作成しておき  

<img src="/images/2019/aws-sftp/sftp-setup06.png" />  

公開鍵を登録する  

<img src="/images/2019/aws-sftp/sftp-setup07.png" />  

作成  

<img src="/images/2019/aws-sftp/sftp-setup08.png" />  

### WinSCP から接続してみる  

<img src="/images/2019/aws-sftp/sftp-setup09.png" />  

と、ここで「unable to assumerole for user」というメッセージが出てログインはできたが S3 に対して読み書きの権限が無いてきなエラーが出る  
今回ユーザーに割り当てた role は面倒だったので EC2 のロールを再利用したのだが、  
Transfer サービスからの信頼関係が設定されてないため権限継承の認証が失敗してたっtぽい  
※新規サービスロール作成から Transfer サービスで作成してれば多分起きないお問題  

下記のように信頼関係を Role に追加する  
SFTPサーバのエンドポイントを VPC で作成したらこんな感じの Role になるのかな  

<img src="/images/2019/aws-sftp/sftp-setup10.png" />  

修正後、エラーなく接続、ファイル転送できた  

<img src="/images/2019/aws-sftp/sftp-setup11.png" />  

WinSCP で「もとのタイムスタンプを保持」設定を有効化していてもエラーになり S3 へのアップロード時刻になる  
バケットでバージョニングしていても、履歴ファイルはもちろん見れない  

すでに S3 へのアクセスクライアントソフトがある状況で、そこそこの金額払ってこの SFTP を利用するメリットあるかな？  
S3 + IAM だけの場合に比べて S3 のポリシーをいじらずに、ログや権限周りを詳細に設定できメリットくらい？  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
