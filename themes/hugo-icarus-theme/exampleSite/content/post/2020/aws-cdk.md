+++
title = "AWS CDK 入門"
tags = ["aws"]
categories = ["SE"]
date = "2020-04-17"
description = "AWS CDKを触ってみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Cloudプロビジョニングツール 
AWS、Microsoft Azure、GoogleCloud Platformなどパブリッククラウドが台頭してきて  
それに追従するよういろいろなプロビジョニングツールが出てきた  

* [pulumi](https://www.pulumi.com/)  
* [Terraform](https://www.terraform.io/)  

その他プロビジョニングのみにとどまらず、SaaSサービスでは色々ある  
ただどれもクラウド基盤側の仕様に左右されたり、追従対応速度にはどうしても上手く行かない部分がある  

マルチクラウドでも利用、検討してない限りは実際に使うクラウド環境をある程度決め打ちして  
そこに適した提供ツールを利用するのがいいんじゃないかな  

* [AWS CloudFormation](https://aws.amazon.com/jp/cloudformation/)  
* [Azure Resource Manager](https://azure.microsoft.com/ja-jp/features/resource-manager/)  
* [Google Cloud Deployment Manager](https://cloud.google.com/solutions/infrastructure-as-code?hl=ja)  

仕事で使う AWS について考えていく  

## AWS CDK
AWSで IaC を行おうとすると CLoudFormation になるが 独自仕様、構文になっていて学習コストがかかる割にはそこまで IaC 感が無い  
そこで登場したのが AWS CDK  

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/cdk/" data-iframely-url="//cdn.iframe.ly/98vkSo7?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

いい感じにマニュアルがあるのでそれに習って進めていく  
<i class="fas fa-external-link-alt"></i> [PREREQUISITES FOR THE WORKSHOP](https://cdkworkshop.com/15-prerequisites.html)  

### IAM アカウントの用意
CDKが利用する AIM アカウントを作成しておく  

<i class="fas fa-chevron-circle-right"></i> 管理ポリシー  

* AWSCloudFormationFullAccess  

<i class="fas fa-chevron-circle-right"></i> インラインポリシー  
CloudFormation でサービスデプロイしようとするとそれなりに強い権限をもたせる必要がある  
CloudFormation は 管理ポリシーでフルアクセス権限を設定し、個別サービスについてはインラインポリシーで権限を限定する  
CloudFormation 経由の場合のみその権限を使えるよう下記を参考にインラインポリシーを設定しておく  
<i class="fas fa-external-link-alt"></i> [AWSサービスによって呼び出されるアクションの最小特権アクセス許可を定義する方法](https://aws.amazon.com/jp/blogs/security/how-to-define-least-privileged-permissions-for-actions-called-by-aws-services/)  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*"
      ],
      "Resource": "*",
      "Condition": {
        "ForAnyValue:StringEquals": {
          "aws:CalledVia": [
            "cloudformation.amazonaws.com"
          ]
        }
      }
    }
  ]
}
```

### CDK のインストール  
作業環境は Windows  

```
npm install -g aws-cdk
```

AWS Credential の設定  
CDKを使うのであれば 別 IAM を用意し、環境毎に使い分ける事になりそうなので profile をちゃんと指定しておく  

```
aws configure --profile hogehoge
AWS Access Key ID [None]: 
AWS Secret Access Key [None]: 
Default region name [None]: ap-northeast-1
Default output format [None]: json
```

cli で利用する AWS Credential を指定しておく  

```
setx AWS_PROFILE hogehoge
```

### IDE環境の準備  
AWS の製品ページで IDE とツールとして下記がおすすめされていたのでインストールしておく  
<i class="fas fa-external-link-alt"></i> [AWS Toolkit for Visual Studio Code](https://aws.amazon.com/jp/visualstudiocode/)  

### プロジェクトの作成
CDKで対応している言語は下記  

* JavaScript, TypeScript (Node.js ≥ 10.3.0)  
* Python (Python ≥ 3.6)  
* Java (Java ≥ 8 and Maven ≥ 3.5.4)  
* .NET (.NET Core ≥ 3.0)  

初期化  

```
mkdir test
cd test
cdk init --language typescript

# Welcome to your CDK TypeScript project!

This is a blank project for TypeScript development with CDK.

The `cdk.json` file tells the CDK Toolkit how to execute your app.

## Useful commands

 * `npm run build`   compile typescript to js
 * `npm run watch`   watch for changes and compile
 * `npm run test`    perform the jest unit tests
 * `cdk deploy`      deploy this stack to your default AWS account/region
 * `cdk diff`        compare deployed stack with current state
 * `cdk synth`       emits the synthesized CloudFormation template

```

git化もしとこうかな  

```
git init
```

初期化された内容は下記のようになっている  

> |- bin/  
> |- lib/  
> |- node_modules/  
> |- test/  
> |- .gitignore  
> |- .npmignore  
> |- cdk.json  
> |- jest.config.js  
> |- package.json  
> |- README.md  
> |- tsconfig.json  

マニュアルに簡単に説明があり  

* `lib/cdk-workshop-stack.ts`  
  CDKアプリケーションのメインスタックが定義される場所  
* `bin/cdk-workshop.ts`  
  CDKアプリケーションのエントリポイント、`lib/cdk-workshop-stack.ts`で定義された内容をロードします  
* `package.json`  
  npmモジュールのマニフェスト  
* `cdk.json`  
  ツールキットにアプリの実行方法を指示  
* `tsconfig.json`  
  プロジェクトのタイプスクリプト設定  

サンプルのアプリケーションスタックファイルを見てみる  

```
import * as cdk from '@aws-cdk/core';

export class StudyStack extends cdk.Stack {
  constructor(scope: cdk.Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);

    // The code that defines your stack goes here
  }
}
```

なんもねぇ  
のでマニュアルのを参考にしておく  

### コードのコンパイル  
TypeScriptにて記述した場合変更を加えるたびにソースをコンパイルする必要がある  
そのため、自動的にコンパイルしてくれるよう監視サービスを実行しておくことも出来る  

監視処理の実行には別ターミナルで立ち上げましょう  

```
npm run watch

[18:40:18] Starting compilation in watch mode...

[18:40:23] Found 0 errors. Watching for file changes.
```

### 準備
bootstrap のインストール  

```
> cdk bootstrap
 ⏳  Bootstrapping environment aws://**************/ap-northeast-1...
CDKToolkit: creating CloudFormation changeset...
 0/2 | 13:13:18 | CREATE_IN_PROGRESS   | AWS::CloudFormation::Stack | CDKToolkit User Initiated
 0/2 | 13:13:21 | CREATE_IN_PROGRESS   | AWS::S3::Bucket | StagingBucket
 0/2 | 13:13:22 | CREATE_IN_PROGRESS   | AWS::S3::Bucket | StagingBucket Resource creation Initiated
 1/2 | 13:13:44 | CREATE_COMPLETE      | AWS::S3::Bucket | StagingBucket
 2/2 | 13:13:46 | CREATE_COMPLETE      | AWS::CloudFormation::Stack | CDKToolkit
 ✅  Environment aws://**************/ap-northeast-1 bootstrapped.
**************************************************
*** Newer version of CDK is available [1.32.2] ***
*** Upgrade recommended                        ***
**************************************************
```

このへんで AWS Cli の Credential 情報も読み込まれてる感じ  

### テストデプロイ
何も無いけどデプロイし、環境設定がちゃんと行えていることを確認する

```
// デプロイ
> cdk deploy

StudyStack: deploying...
StudyStack: creating CloudFormation changeset...
 0/2 | 13:24:40 | CREATE_IN_PROGRESS   | AWS::CloudFormation::Stack | StudyStack User Initiated
 0/2 | 13:24:44 | CREATE_IN_PROGRESS   | AWS::CDK::Metadata | CDKMetadata
 0/2 | 13:24:46 | CREATE_IN_PROGRESS   | AWS::CDK::Metadata | CDKMetadata Resource creation Initiated
 1/2 | 13:24:46 | CREATE_COMPLETE      | AWS::CDK::Metadata | CDKMetadata
 2/2 | 13:24:48 | CREATE_COMPLETE      | AWS::CloudFormation::Stack | StudyStack

 ✅  StudyStack

Stack ARN:
arn:aws:cloudformation:ap-northeast-1:778686969569:stack/StudyStack/57bc0940-8063-11ea-9abd-0693e5f01c30
```

なにか出来たらしい  
まず CDK の CloudFormation 用 S3 を作成する Stack が勝手に作成される  

<img src="/images/2020/aws/cdk/cdk-01.png" />  

次にプロジェクト用の Stack 今回は空っぽの Stack が作成されている  

<img src="/images/2020/aws/cdk/cdk-02.png" />  

なるほどだから Bootstrap 実行時 何も定義したリソースが無いのに 2個 ごにょごにょ処理が動いてたのね  

## コードを書く 
実際にS3を作ってみる  
AWS CDK で提供されているAPIやライブラリについては下記で確認  
<i class="fas fa-external-link-alt"></i> [AWS CDK API Reference](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-construct-library.html)  

S3ライブラリをインストール  
プロジェクト作成時にデフォルトでインストールされていると思います  

```
npm i @aws-cdk/aws-s3
```



```
// スタックを指定してデプロイ
$ cdk deploy ${StackName} 

// CloudFormationのテンプレートファイル生成
$ cdk synth

// CloudFormationのテンプレートファイル生成してファイルに書き出す
$ cdk synth --output ./output

// 差分を確認
$ cdk diff
```
