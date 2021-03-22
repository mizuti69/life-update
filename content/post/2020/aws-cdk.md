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
        "cloudformation:CreateStack",
        "cloudformation:Describe*",
        "cloudformation:Validate*",
        "cloudformation:Update*",
        "cloudformation:List*",
        "cloudformation:GetT*",
        "cloudformation:CreateChangeSet",
        "cloudformation:ExecuteChangeSet",
        "cloudformation:DeleteChangeSet",
        "iam:Get*",
        "iam:Create*",
        "iam:Attach*",
        "iam:Add*",
        "iam:PassRole",
        "iam:DeleteRole",
        "iam:DetachRolePolicy",
        "iam:PutRolePolicy",
        "iam:DeleteRolePolicy",
        "iam:RemoveRoleFromInstanceProfile",
        "iam:DeleteInstanceProfile",
        "logs:*",
        "ssm:Get*"
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

CDK、どんな環境でも標準的に必要になりそうな権限を CDK ロールみたいの作っておいて  
環境毎に必要な各種他サービスの権限は別ロールかグループで付与してって管理したほうが使い勝手良さそう  

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

構文エラーもここでわかるので基本的にはずっと立ち上げておく  

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
bootstrap を実行すると AWS 上に **CDKToolkit** という CloudFormationStack と S3 のバケットが作成される  

<img src="/images/2020/aws/cdk/cdk-01.png" />  

<img src="/images/2020/aws/cdk/cdk-02.png" />  

### CDK Package.json の修正
CDKは今現在も開発が頻繁でマイナーバージョンが毎週のように変わる  
なのでCDK書きながら次の週に新しいサービスを定義しようとモジュール追加するとバージョンがずれてエラーが出たりする  
なのでそうならないよう `package.json` で CDK関連のバージョンは固定するか、常にアップデートするようにしておく  
※デフォルトで CDK **だけ** インストール時の固定バージョンになってるのが悪い  

ここでは検証なので常に最新化 `^` するようにしておく  

```
$ vim package.json

  "devDependencies": {
    "@aws-cdk/assert": "^1.54.0",
    "@types/jest": "^25.2.1",
    "@types/node": "10.17.5",
    "aws-cdk": "^1.53.0",
    "jest": "^25.5.0",
    "ts-jest": "^25.3.1",
    "ts-node": "^8.1.0",
    "typescript": "~3.7.2"
  },
  "dependencies": {
    "@aws-cdk/aws-ec2": "^1.54.0",
    "@aws-cdk/aws-efs": "^1.54.0",
    "@aws-cdk/aws-elasticloadbalancingv2": "^1.54.0",
    "@aws-cdk/aws-elasticloadbalancingv2-targets": "^1.54.0",
    "@aws-cdk/aws-iam": "^1.54.0",
    "@aws-cdk/aws-s3": "^1.54.0",
    "@aws-cdk/core": "^1.54.0",
    "source-map-support": "^0.5.16"
  }
```

コードを書いて行く中でバージョンが変わってったら都度都度アップデートして対応しいく  

```
> npm update -g aws-cdk
> npm update
```

## コードを書く 
<i class="fas fa-external-link-alt"></i> [AWS CDK API Reference](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-construct-library.html)  
CDKのドキュメントを見ながら EC2 を立ててみる  

### AWSアカウント情報の読み込み  
新規でまっさらな状況から作るというわけでもない限り、既存のAWSアカウントと環境情報を読み込ませる  

```
$ vim bin/test.ts

const app = new cdk.App();
new TestStack(app, 'TestStack', {
    // import aws account info
    env: {
        account: process.env.CDK_DEFAULT_ACCOUNT,
        region: process.env.CDK_DEFAULT_REGION
    }
});
```

### キーペアの作成
EC2で利用するキーペアは事前にコンソールで作成、登録しておく  

### VPCの作成  
VPCはすでにあるVPC上に立てたいので既存の環境を利用する  

```
$ vim lib/test-stack.ts

    // Import VPC
    let myVpc = ec2.Vpc.fromLookup(this, 'VPC', { vpcId: 'vpc-****' });
    // Import Subet
    let myPrivateSubnet1 = ec2.Subnet.fromSubnetAttributes(this, 'PrivateSubnet1', {
      subnetId: 'subnet-****',
      availabilityZone: 'ap-northeast-1c',
      routeTableId: 'rtb-****',
    });
    let myPrivateSubnet2 = ec2.Subnet.fromSubnetAttributes(this, 'PrivateSubnet2', {
      subnetId: 'subnet-****',
      availabilityZone: 'ap-northeast-1a',
      routeTableId: 'rtb-****',
    });
```

### セキュリティグループの作成
EC2用の新規セキュリティグループを作成  

```
$ vim lib/test-stack.ts

    // Create Ec2 security group
    let mySecurityGroup = new ec2.SecurityGroup(this, 'SecurityGroup', {
      vpc: myVpc,
      description: `${projectName} Server Side SecurityGroup`,
      securityGroupName: `${projectName}-ec2-sg`,
      allowAllOutbound: true
    });
    mySecurityGroup.addIngressRule(ec2.Peer.ipv4('172.25.0.0/16'), ec2.Port.allTcp(), 'All VPC');
```

### EC2用サービスロールの作成
EC2で利用するサービスロールを作成

```
$ vim lib/test-stack.ts

    // Create Ec2 service role
    let myRole = new iam.Role(this, 'Ec2role', {
      assumedBy: new iam.ServicePrincipal('ec2.amazonaws.com'),
      roleName: `${projectName}-ec2-role`,
    });
```

### AMIの定義  
EC2で利用するAMIを定義  
ここでは公式の AmazonLinux2 を利用  

```
$ vim lib/test-stack.ts

    // Set AMI
    let myAMI = new ec2.AmazonLinuxImage({
      generation: ec2.AmazonLinuxGeneration.AMAZON_LINUX_2,
      edition: ec2.AmazonLinuxEdition.STANDARD,
      virtualization: ec2.AmazonLinuxVirt.HVM,
      storage: ec2.AmazonLinuxStorage.GENERAL_PURPOSE,
    });
```

似たようなので `ec2.MachineImage.latestAmazonLinux` もありこちらは**常に**最新のAMIを利用したい場合に利用する  
もちろん常になので新シAMIのバージョンが出たら deploy したタイミング等で EC2 の置き換えが発生する  
こちらを利用したい場合はそもそもの EC2 運用からちゃんと考えよう  

じゃないと事故る（事故った）  
怖い人は CDK で EC2 作成後に**削除保護**を必ず有効化しておこう  


### EC2の作成
EC2作成したいだけなのに VPC、IAM、等々一通りの準備に手間がかかる  
最後に 使う KeyPair、インスタンスタイプ、EBSのサイズを指定  

```
$ vim lib/test-stack.ts

    // InstanceType
    let myInstanceType = new ec2.InstanceType(ec2InstanceType);

    // Keypair
    let myKey = keyPair;

    // EBS
    let myEBS = ec2.BlockDeviceVolume.ebs(50);

    // Create EC2
    let myInstance1 = new ec2.Instance(this, 'Instance1', {
      instanceType: myInstanceType,
      machineImage: myAMI,
      vpc: myVpc,
      instanceName: `${projectName}-01`,
      keyName: myKey,
      role: myRole,
      securityGroup: mySecurityGroup,
      blockDevices: [
        {
          deviceName: '/dev/xvda',
          volume: myEBS,
        }
      ],
      vpcSubnets: {
        subnets: [myPrivateSubnet1],
      },
    });
    myInstance2.addUserData(
      "yum check-update -y",
      "yum upgrade -y",
    );
    myRole.addManagedPolicy(iam.ManagedPolicy.fromAwsManagedPolicyName('AmazonEC2FullAccess'));
    myRole.addManagedPolicy(iam.ManagedPolicy.fromAwsManagedPolicyName('service-role/AmazonEC2RoleforSSM'));
```

7/30 ここで EC2 の削除保護有効化とか出来ればいいんだけど標準APIの方には無いみたいだ  

また CDK のエラーは本当に親切じゃない  
IAM の権限が足らない場合はある程度わかるようになってるが、それ以外は下記のようなエラーになっていて何が問題か分からない  

```
The requested configuration is currently not supported. Please check the documentation for supported configurations. (Service: AmazonEC2; Status Code: 400; Error Code: Unsupported; Request ID:  
57477a70-e44e-4f3c-b606-2ab180098516)
```

例えば上記の問題は Cfn ではまだ東京リージョンではサポートされていないインスタンスタイプを指定したためだった  
※リージョン自体には対応していてコンソール上は選択できる  

こういうのが出ると調査にめやくちゃ時間かかるので将来に期待したい  
※同様に AWS Backup は Vault の作成でどうにもこうにもコケるため諦めた  

### Taging

```
$ vim lib/test-stack.ts

    // Taging
    cdk.Tag.add(this, tagKey, tagValue);
```

ここまで定義して `npm run watch` 上でエラーがなければデプロイに進める  

## デプロイ
CDKの定義を Cfn 化  

```
> cdk synth
```

ここでも構文チェックを行ってくれる  
そしてデプロイされる内容、変更箇所を確認し  

```
> cdk diff
```

問題なければデプロイ  

```
> cdk deploy
```

---
7/30 中途半端に下書きしていたやつを間違って公開してしまっていたため加筆修正  


基本は CloudFormation を好きな言語で書ける！なので Cfn 自体のメリット、デメリットはそのままある  
シンプルな分変数の再利用や環境別の分け方などは自身で上手くやる方法を見つけるしか無い  
`-c` オプションがあるのでなんとかはなりそうだけど  

とりあえず Typescript らしさはかけらもないベタ書きになってしまっているのでなんとかこのへんはスマートにしたいな  

とにかくまだ絶賛開発中という印象  
毎週リリースがあるのもちろん、開発中の定義だよというのがいろんなサービスのAPIにあるし  
ドキュメント自体もそこまで親切じゃない  

CDKの記事とか調べてると Cfn～ のクラスを利用している人が多いがそちらのほうが安定しているし  
Cfn ベースなので定義できることもなんかエラーで出来ないという事が少ないからのよう  
でもそれだったら CDK の価値が...と思って意地でもなるべく使わないようにしてみた  

取り急ぎコアサービスはこれで管理、コピペできるようにしつつ
細かいところは今まで通りコンソールで操作しつつ適宜置き換えていく感じかなー  

触ってみた印象結局 Terraform とかでいいんじゃねと思ってもいる  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="https://www.amazon.co.jp/AWS%E8%AA%8D%E5%AE%9A%E3%82%BD%E3%83%AA%E3%83%A5%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%88-%E3%83%97%E3%83%AD%E3%83%95%E3%82%A7%E3%83%83%E3%82%B7%E3%83%A7%E3%83%8A%E3%83%AB-%E8%A9%A6%E9%A8%93%E7%89%B9%E6%80%A7%E3%81%8B%E3%82%89%E5%B0%8E%E3%81%8D%E5%87%BA%E3%81%97%E3%81%9F%E6%BC%94%E7%BF%92%E5%95%8F%E9%A1%8C%E3%81%A8%E8%A9%B3%E7%B4%B0%E8%A7%A3%E8%AA%AC-%E5%B9%B3%E5%B1%B1-%E6%AF%85/dp/4865942483/ref=as_li_ss_il?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=aws&qid=1594296050&sr=8-4&linkCode=li3&tag=sinokyoufu-22&linkId=b4975cf82152d3c6e34bf1c47773d412&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4865942483&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li3&o=9&a=4865942483" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}
