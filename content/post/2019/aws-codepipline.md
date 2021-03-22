+++
title = "AWS CodePipline + Commit + Deploy で自動生活検証"
tags = ["aws"]
categories = ["SE"]
date = "2019-07-26"
description = "AWS CpdePipline を利用して CodeCommit + CodeDeploy で自動アプリケーション更新リリースを行う検証"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## CodeCommit
<i class="fas fa-external-link-alt"></i> [AWS CodeCommit とは](https://docs.aws.amazon.com/ja_jp/codecommit/latest/userguide/welcome.html)  

### 料金
<i class="fas fa-external-link-alt"></i> [AWS CodeCommit の料金](https://aws.amazon.com/jp/codecommit/pricing/)  
月あたりのアクティブユーザー数単位で金額が来ます  
アクティブユーザー数が増えると特典の使用量も増える  

### リポジトリの作成
<img src="/images/2019/aws-codepip/codecommit_create01.png" />  
<img src="/images/2019/aws-codepip/codecommit_create02.png" />  

### IAMの設定
Codecommit に HTTPS 経由でコミットを行う IAM ユーザーに認証情報を作成する  

<img src="/images/2019/aws-codepip/codecommit_create03.png" />  
<img src="/images/2019/aws-codepip/codecommit_create04.png" />  
<img src="/images/2019/aws-codepip/codecommit_create05.png" />  

### 認証連携
作成したリポジトリを clone し、認証情報に先程作成した情報を入力するとアクセスできる  
デプロイしたい資産をプッシュしておく  

<img src="/images/2019/aws-codepip/codecommit_create06.png" />  

デフォルトだと CodeCommit リポジトリ自体にアクセス制御的な機能が見当たらない  
CodeCommit へアクセスする IAM ユーザー側で権限設定を行う必要があるようで、 CodeCommit のアカウントはちゃんと分けて発行したほうがよさそう  
<i class="fas fa-external-link-alt"></i> [CodeCommit の権限リファレンス](https://docs.aws.amazon.com/ja_jp/codecommit/latest/userguide/auth-and-access-control-permissions-reference.html)  

## CodeDeploy
<i class="fas fa-external-link-alt"></i> [CodeDeploy とは](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/welcome.html)  

### 料金
<i class="fas fa-external-link-alt"></i> [AWS CodeDeploy の料金](https://aws.amazon.com/jp/codedeploy/pricing/)  
EC2/Lambda での CodeDeploy: AWS CodeDeploy を使用した Amazon EC2 や AWS Lambda へのコードデプロイに追加料金はない  

### デプロイサーバ設定
CodePipline からデプロイ指定するためのデプロイグループのみ作成する  
今回は EC2 にデプロイするためサーバに Agent をインストールする  
事前準備として AWS Cli 、 Ruby のインストールを行っておく、Rubyのバージョンは指定があるので注意  

> Currently recommanded Ruby versions are 2.0.0, 2.1.8, 2.2.4 and 2.3.0, and 2.4.0.

また 実行する EC2 インスタンズ上から S3 にアクセスできるように `AmazonS3ReadOnlyAccess` ポリシーを最低限付与しておく  
<i class="fas fa-external-link-alt"></i> [Amazon Linux または RHEL 用の CodeDeploy エージェントのインストールまたは再インストール](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/codedeploy-agent-operations-install-linux.html)  

```
# wget https://aws-codedeploy-ap-northeast-1.s3.ap-northeast-1.amazonaws.com/latest/install
# ./install auto
# systemctl status codedeploy-agent
```

### デプロイ指示ファイルの作成
デプロイ情報を記載したファイルを作成しソースのルートに入れておく  

```
# appspec.yml
version: 0.0
os: linux
files:
  - source: /
    destination: /opt/mkdocs
```

### Deploy IAM Role の設定  
AWS CodeDeploy で利用する IAM Role を作成しておく  
<i class="fas fa-external-link-alt"></i> [CodeDeploy のサービスロールを作成する](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/getting-started-create-service-role.html)  

<img src="/images/2019/aws-codepip/codedep_create01.png" />  

### Deployグループの作成
アプリケーション設定  
<br />
<img src="/images/2019/aws-codepip/codedep_create02.png" />  
<img src="/images/2019/aws-codepip/codedep_create03.png" />  
<br />

グループ設定  
<br />
<img src="/images/2019/aws-codepip/codedep_create04.png" />  
<img src="/images/2019/aws-codepip/codedep_create05.png" />  
<img src="/images/2019/aws-codepip/codedep_create06.png" />  
<img src="/images/2019/aws-codepip/codedep_create07.png" />  
<img src="/images/2019/aws-codepip/codedep_create08.png" />  
<br />

デプロイ設定は以下から選択できる  
<i class="fas fa-chevron-circle-right"></i> CodeDeployDefault.AllAtOnce  
一度に可能な限り多くのインスタンスへアプリケーションリビジョンをデプロイするよう試みます。  

<i class="fas fa-chevron-circle-right"></i> CodeDeployDefault.HalfAtATime  
一度に最大半分のインスタンスにデプロイします (端数は切り捨てられます)。  
デプロイ全体は、アプリケーションリビジョンが少なくとも半分のインスタンスにデプロイされた場合は成功です (端数は切り捨てられます)。  

<i class="fas fa-chevron-circle-right"></i> CodeDeployDefault.OneAtATime  
一度に 1 つのインスタンスにのみアプリケーションリビジョンをデプロイします。  

## CodePupline
<i class="fas fa-external-link-alt"></i> [AWS CodePipeline とは](https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/welcome.html)  
デプロイしたい資産を CodeCommit に登録し、 資産をデプロイしたいサーバ登録を CodeDeploy に登録し、２つを CodePipline でつなげます  

### 料金
<i class="fas fa-external-link-alt"></i> [AWS CodePipeline 料金](https://aws.amazon.com/jp/codepipeline/pricing/)  
月あたりのアクティブな1パイプライン単位で金額が発生、１パイプラインは無料です  

### Piplineの作成
<img src="/images/2019/aws-codepip/codepip_create01.png" />  
<img src="/images/2019/aws-codepip/codepip_create02.png" />  

ソース元の指定  
CodeCommit の他に S3 、 Github 等も指定できる  
事前に作成した CodeCommit のリポジトリを指定する  
<br />
<img src="/images/2019/aws-codepip/codepip_create03.png" />  
<img src="/images/2019/aws-codepip/codepip_create04.png" />  

ブルドステージ  
今回はゔビルドが必要なアプリケーションでは無いためスキップ  

<img src="/images/2019/aws-codepip/codepip_create05.png" />  


事前に作成したデプロイグループを指定  

<img src="/images/2019/aws-codepip/codepip_create06.png" />  


作成すると S3 バケットも一緒に作られるっぽい  

<img src="/images/2019/aws-codepip/codepip_create07.png" />  


### 実行
初回は自動実行される  
意外と時間かかる、成功するとこんなかんじ  

<img src="/images/2019/aws-codepip/codepip_create08.png" />  


デプロイ中は LB 設定をしていた場合メンバーサーバに対して unhelty になっていた  
デプロイ中の LB 設定を有効化していた場合、 LB へのトラフィックアウト、トラフィックインは LB のヘルスチェック設定に依存するため  
デプロイが遅い時は LB のヘルスチェック設定を調整する必要がある（数分～数十分違いが出る）  
それもやりたくない場合はデプロイ時の LB 設定を無効化して、 サイト表示は手動でコントロールするようにする必要がある  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}