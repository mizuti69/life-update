+++
title = "AWS Amplify で Docusaurus を動かしてみる"
tags = ["aws"]
categories = ["SE"]
date = "2021-05-27"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

本当は AppRunner を使いたかったけれど、ソースが Github か コンテナ(ECR) だったので、諸事情により泣く泣く Amplify で検討する。  

<!--more-->

## AWS Amplify

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://aws.amazon.com/jp/amplify/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:a0.awsstatic.com/libra-css/images/logos/aws_logo_smile_1200x630.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">AWS Amplify（アプリケーションの構築とデプロイ）| AWS</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">AWS Amplify は、ウェブやモバイルのフロントエンドデベロッパーが、安全でスケーラブルなフルスタックアプリケーションを構築するためのサービスです。iOS、Android、JavaScript、Flutter、React などをサポートしています。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

イメージとしては AppRunner 含め Netlify、Vercel みたいなことができるよというサービス。  
個人利用であれば上記無料利用枠でホスティングサービスを利用したほうが良いだろう、AWS Amplify を使うメリットは AWS 内で使えるという点。  

### 料金
料金表にある通り。  
<i class="fa fa-external-link" aria-hidden="true"></i> [AWS Amplify 料金](https://aws.amazon.com/jp/amplify/pricing/?nc=sn&loc=3)  

ビルド・デプロイ処理時間単位の重量課金と、ホスティングルトレージ、ホスティングサービス、これはトラフィックに近いイメージかな？  
上記にはビルド元の料金は含まれてないので CodeCommit を利用する場合はまた別途かかる。  

また Amplify には下記のようにアプリケーション数やドメイン、ブランチ数、サイズ等の制限があるので確認しておく。  
<i class="fa fa-external-link" aria-hidden="true"></i> [AWS Amplify - サービスクォータ](https://docs.aws.amazon.com/ja_jp/general/latest/gr/amplify.html)  

## Docusaurus の準備
Docusaurus については本家を参照。  
<i class="fa fa-external-link" aria-hidden="true"></i> [Docusaurus - Installation](https://docusaurus.io/docs/installation)  

とりあえずローカルにインストールし  

```
> npx @docusaurus/init@latest init my-website classic
```

CodeCommit を用意してそこに配置する。  

## リポジトリの用意
CodeCommit 上にリポジトリを用意し  

<img src="/images/2021/aws/amplify/amplify-01.png" />  

リポジトリに対して利用する IAM ユーザーに権限、認証情報を用意する。  
<i class="fa fa-external-link" aria-hidden="true"></i> [Git 認証情報を使用した HTTPS ユーザーのセットアップ](https://docs.aws.amazon.com/ja_jp/codecommit/latest/userguide/setting-up-gc.html?icmpid=docs_acc_console_connect_np)  

インストールした素の Docusaurus をプッシュする。  

## Amplify のセットアップ

### Host your web app
先程作成した CodeCommit リポジトリを選択。  
検証なのでブランチは master そのまま、開発環境と分けて利用していきたい場合はブランチ戦略に合わせて指定する。  

<img src="/images/2021/aws/amplify/amplify-02.png" />  
<img src="/images/2021/aws/amplify/amplify-03.png" />  

### ビルド設定の構成
ビルド設定。デフォルトで JS 用のビルド定義が選定されているので、Docusaurus に合わせて `commands` や `baseDirectory` を変更する。  

<img src="/images/2021/aws/amplify/amplify-04.png" />  

```
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - yarn install
    build:
      commands:
        - yarn run build
  artifacts:
    # IMPORTANT - Please verify your build output directory
    baseDirectory: build/
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

その他オプションとして指定のビルド用 Docker を指定したり、環境変数を指定できる。  
この辺は Codeシリーズ でおなじみだね。  

<img src="/images/2021/aws/amplify/amplify-05.png" />  

### デプロイ
設定内容を確認して問題なければごーごー！  

<img src="/images/2021/aws/amplify/amplify-06.png" />  

完了すると下記のような画面になり、右下の amplify.app URL からサイトの表示を確認できる。  
画面だけみてると Netlify 等々と大きく違いはなく、似たいような体験でアプリをデプロイできると思う。  

<img src="/images/2021/aws/amplify/amplify-07.png" />  
<img src="/images/2021/aws/amplify/amplify-08.png" />  

Amplify 自体はバックエンドに S3 の Web ホスティング、CloudFront を利用してるとのことだが、各サービスページにアクセスしてもそれっぽいのが見つからなかったので、ユーザー側でカスタマイズや設定の変更等操作はできなさそうだ。  

## Admin UI
Amplify はここまでの機能でも  

* カスタムドメイン設定
* ビルド設定
* メール設定
* Webhook
* リダイレクト設定
* カスタムヘッダ
* アクセスコントロール（ベーシック認証）

と十分機能があるが、Admin UI を利用することでさらに、アプリに対する細かい権限設定や機能追加等ができるようになる。  
<i class="fa fa-external-link" aria-hidden="true"></i> [AWS Amplify Admin UI: アプリケーションのバックエンド開発を支援し、クラウドの経験を必要としない管理ツール](https://aws.amazon.com/jp/blogs/news/aws-amplify-admin-ui-helps-you-develop-app-backends-no-cloud-experience-required-jp/)  

追加料金はかからない・・・のかな？記載がないのでわからない。でもバックエンドで実は○○使ってるのでそちら料金はかかりますとかありそう。  
Admin UI を利用すると、追加で欲しい機能を簡単なコードでAWSサービスと連携して追加できる。という感じらしい。  

ただちょっと環境依存度が上がってしまうので、どうしても、という場合に使う感じかな？  
Amplify 便利だけど当然小回り利かそうとすると当然手間がかかるので、要検討かな。  
