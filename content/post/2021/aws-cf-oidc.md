+++
title = "AWS CloudFront で Google認証を利用する"
tags = ["aws"]
categories = ["SE"]
date = "2021-05-28"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

前回 Amplify で簡単に SSG のビルド、デプロイができたが、認証周りを追加しようとするとアプリのAWS依存度が上がってしまうので、しょうがないので複雑に作ってみる。  

<!--more-->

## Lambda@Edge を利用して認証処理を組み込む  
今回の用途的にアプリに認証機能をもたせたいのではなく、サイトへのアクセスは問答無用で認証処理入れたい  
アプリ自体がSSGとした場合、S3のWebホスティング、CloudFront という構成自体は Amplify とやってることは同じで問題はなく、  
CloudFront で認証処理を挟もうとすると Lambda@Edge でできるみたい。  

他 ALB をリダイレクトサーバ的に使う手法も有るみたいだけど、料金的には Lambda@Edge の方がやすそうだし、ALBの使い方としてそれはあんまり推奨されない気がするので。  

### 環境の用意
ソースは CodeCommit で管理して、pipline で S3 にデプロイする Amplify と同じ。  
S3 のホスティング、CloudFront の用意、CodePipline は昔やったので割愛する。  

* [Amazon S3 を使用して静的ウェブサイトをホスティングする](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/userguide/WebsiteHosting.html)  
* [CloudFront ディストリビューションを使用して Amazon S3 バケットへのアクセスを制限する方法を教えてください。](https://aws.amazon.com/jp/premiumsupport/knowledge-center/cloudfront-access-to-amazon-s3/)  
* [CodeBuild のビルド仕様に関するリファレンス](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/build-spec-ref.html)  

### Lambda@Edge 関数の用意
下記の記事を参考に、そのままやってみる  
<i class="fa fa-external-link" aria-hidden="true"></i> [CloudFront で Lambda@Edge を使って OpenID Connect (OIDC) 認証する](https://qiita.com/yh1224/items/2cc440f6fe0fef1502b7)  

```
# git clone https://github.com/Widen/cloudfront-auth.git
# cd cloudfront-auth
```

関数は nodejs で作成されるため、node、npm、zip コマンドをインストールしておく。  

```
# ./build.sh
>: Enter distribution name: study-cf-auth // 任意の関数名
>: Authentication methods:
    (1) Google
    (2) Microsoft
    (3) GitHub
    (4) OKTA
    (5) Auth0
    (6) Centrify
    (7) OKTA Native

    Select an authentication method:  1 // 利用したい認証プロバイダ  
>>: Client ID:  // 作成した Google OAuth の ClientID
>>: Client Secret:  // 作成した Google OAuth の Clien Secret Key
>>: Redirect URI: https://xxxxxxxxxxxx/_callback // 作成した Google OAuth に設定したリダイレクトURL
>>: Hosted Domain: hogehoge.co.jp // 認証をメールドメイン等で制限したい場合は指定
>>: Session Duration (hours):  (0) 24 // セッションの有効時間
>>: Authorization methods:
   (1) Hosted Domain - verify email's domain matches that of the given hosted domain
   (2) HTTP Email Lookup - verify email exists in JSON array located at given HTTP endpoint
   (3) Google Groups Lookup - verify email exists in one of given Google Groups

   Select an authorization method:  1 // 認証制限の指定
Done... created Lambda function distributions/study-cf-auth/study-cf-auth.zip
```

作成すると 上記の様に関数が ZIP ファイルで作成される。  

### Lambda関数の作成
CloudFront で利用する Lambda@Edge は バージニア北部 `us-east-1` に作成する必要がある。  
このツールでは SAM 用の template.yml が参考に置いてあるが、手動で作成する場合は Git に記載のある手順でも作成できる。  
<i class="fa fa-external-link" aria-hidden="true"></i> [Github - Widen/cloudfront-auth](https://github.com/Widen/cloudfront-auth/wiki/Manual-Deployment)  

関数の作成より、設計図から **cloudfront-http-redirect** を選択して作成する。  

<img src="/images/2021/aws/cloudfront/lambda_edge-01.png" />  

タイムアウト値は 5秒にすると良いとのこと。  

あとは作成した ZIP をアップロードしてコードを更新し、トリガーとして CloudFront との連携設定を行う。  
CloudFront のトリガータイプは **ビュワーリクエスト**  

<img src="/images/2021/aws/cloudfront/lambda_edge-02.png" />  

更新してデプロイ後、反映までには数十分かかる場合もあるので注意。  

完了後、 Google 認証が出てくることを確認できれば成功。  

---

注意点があるとすれば、 CloudFront + Lambda@Edge において ViewerRequest の処理は 1Behavier あたり１つしか設定出来ないため、  
S3 + CloudFront 構成とする場合ディレクトリインデックス問題が出てくる。  
S3 を Webホスティングすれば良いんだけどそうすると S3 に対するアクセス制御が難しい。  

S3 + CloudFront 構成って便利なんだけどフロントサイトとして使おうとすると色々妥協が必要になる。画像ホスティングとして利用されたり、簡易リダイレクトサーバとしてよく利用されている理由がわかる。  
