+++
title = "AWS CloudFormation Former を使ってみる"
tags = ["aws"]
categories = ["se"]
date = "2019-11-05"
description = "AWSの CloudFormation 定義をを既存の作成済みリソースから取得・バックアップしてみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## CloudFormation
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/cloudformation/" data-iframely-url="//cdn.iframe.ly/8657z8N?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

AWS CloudFormation に対する追加料金はありません  
やったね  

## 既存リソースのCloudFormation化
AWSの構成バックアップを考えたとき、Terraformでも良いけどAWSの仕様に振り回される可能性があり  
CloudFormer(β) を使用して既存リソースをテンプレート化する  

### Former Stack の作成
<i class="fas fa-external-link-alt"></i> [CloudFormer (ベータ) を使用して既存の AWS リソースから AWS CloudFormation テンプレートを作成する](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/cfn-using-cloudformer.html)  

CloudFormation の stack を作成する  

<img src="/images/2019/aws-cloudformation/former_create01.png" />  

「サンプルテンプレートを使用」からサンプルテンプレートの「Cloudformer」を指定する  

> This setting will control if the Cloudformer Web server will launch in the default VPC or if a new VPC will be created, or if you wish to launch into an existing non-default VPC.

CloudFromerは動作に VPC が必要らしい デフォルトは新規になっているけどデフォルトを default にしてみる  

<img src="/images/2019/aws-cloudformation/former_create02.png" />  

CloudFormer を利用するためのユーザー情報を登録する  

<img src="/images/2019/aws-cloudformation/former_create03.png" />  
<img src="/images/2019/aws-cloudformation/former_create04.png" />  

数分待つと stack が作成されている  

<img src="/images/2019/aws-cloudformation/former_create05.png" />  

> AWS CloudFormer Beta - template creation prototype application. This tool allows you to create an AWS CloudFormation template from the AWS resources in your AWS account. **Warning** This template creates a single EC2 instance in your account to run the application - you will be billed for the instance at normal AWS EC2 rates.  

Former を利用すると EC2 が作成されるらしい、だから VPC 作成なんて言ってたのね  
EC2 のコンソールを見てみると、Cloudformation を利用しているリージョンと同リージョンに EC2 が新規で作成されてた  

> CloudFormer スタックによって t2.medium Amazon EC2 インスタンスが起動されます。このスタックは、テンプレート作成後、ウォークスルーの最後に削除します。

<img src="/images/2019/aws-cloudformation/former_create06.png" />  

注意しなければいけないのはインスタンスのインバウンドポリシーが 443 Any で空いてること  
Former を使うときは必ずサービス利用VPCではなく新規作成とかにしておいたほうが良い  
それ以外は無料利用枠の範囲にできるだけ収まるようなインスタンスになっている  

### Stack の起動  
Stack が作成されると出力タブのURLから Former にアクセスできる  

<img src="/images/2019/aws-cloudformation/former_create07.png" />  

なるほどね、Formation 経由で EC2 作成してその中に Former アプリをデプロイしてるのかな？  
アクセスすると Stack 作成時の ID/PASS でログインを促される  

<img src="/images/2019/aws-cloudformation/former_create08.png" />  
<img src="/images/2019/aws-cloudformation/former_create09.png" />  

テンプレートを作成したいリソースがあるリージョンを選択して「Create Template」  
作成するテンプレートの説明を記入し、リソースのフィルタは入れずに次に進む  

<img src="/images/2019/aws-cloudformation/former_create10.png" />  

それぞれのステップで選択できるリソース内容（記事作成時点）  

<i class="fas fa-chevron-circle-right"></i> DNS  

* Route 53 Hosted Zones  
* Route 53 DNS Records  
* Route 53 Health Checks  

<i class="fas fa-chevron-circle-right"></i> VPC  

* Amazon Virtual Private Clouds (VPCs)  

<i class="fas fa-chevron-circle-right"></i> VPC Network  
VPCを選択していると、関連するリソースは自動でチェックが入っている  

* Amazon Virtual Private Cloud (VPC) Subnets  
* Amazon Virtual Private Cloud (VPC) Internet Gateways  
* Amazon Virtual Private Cloud (VPC) Customer Gateways  
* Amazon Virtual Private Cloud (VPC) VPN Gateways  
* Amazon Virtual Private Cloud (VPC) DHCP Options  
* Amazon Virtual Private Cloud (VPC) VPN Connections  
* Amazon Virtual Private Cloud (VPC) Peering Connections  

<i class="fas fa-chevron-circle-right"></i> VPC Security  
VPCを選択していると、関連するリソースは自動でチェックが入っている  

* Amazon Virtual Private Cloud (VPC) Network ACLs  
* Amazon Virtual Private Cloud (VPC) Route Tables  

<i class="fas fa-chevron-circle-right"></i> Network  

* Elastic Load Balancers（**ALBは無い**）  
* Amazon EC2 Elastic IP Addresses  
* Amazon EC2 Network Interfaces  
* Amazon CloudFront Distributions  

<i class="fas fa-chevron-circle-right"></i> Managed Services  

* Auto Scaling Groups  
* Elastic Beanstalk Applications  
* OpsWorks Stacks  

<i class="fas fa-chevron-circle-right"></i> Managed Configuration  

* Auto Scaling Launch Configurations  
* Elastic Beanstalk Application Versions  
* Elastic Beanstalk Environments  
* Elastic Beanstalk Configuration Templates  
* OpsWorks Apps  
* OpsWorks Layers  
* OpsWorks Elastic Load Balancer Attachements  

<i class="fas fa-chevron-circle-right"></i> Compute  

* Amazon EC2 Instances  
* OpsWorks Instances  

<i class="fas fa-chevron-circle-right"></i> Storage  

* Amazon Elastic Block Storage Volumes  
* Amazon RDS Database Instances  
* Amazon ElastiCache Cache Clusters  
* Amazon Redshift Clusters  
* Amazon DynamoDB Tables  
* Amazon S3 Buckets  
* Amazon SimpleDB Domains  

<i class="fas fa-chevron-circle-right"></i> Storage Config  

* Amazon RDS DB Subnet Groups  
* Amazon RDS DB Parameter Groups  
* Amazon ElastiCache Subnet Groups  
* Amazon ElastiCache Parameter Groups  
* Amazon RedShift Cluster Subnet Groups  
* Amazon Redshift Cluster Parameter Groups  

<i class="fas fa-chevron-circle-right"></i> Application Services  

* Amazon SQS Queues  
* Amazon SNS Topics  
* Amazon Kinesis Streams  

<i class="fas fa-chevron-circle-right"></i> Security Groups  

* Amazon EC2 Security Groups  
* Amazon RDS Security Groups  
* Amazon ElastiCache Security Groups  
* Amazon Redshift Cluster Security Groups  
* Amazon SQS Queue Policies  
* Amazon SNS Topic Policies  
* Amazon S3 Bucket Policies  

<i class="fas fa-chevron-circle-right"></i> Operational Resources  

* Auto Scaling Policies  
* Auto Scaling Scheduled Actions  
* CloudWatch Alarms  
* CloudTrail Trails  


サマリー画面  
<img src="/images/2019/aws-cloudformation/former_create11.png" />  

問題なければテンプレートを S3 に保存できる  
画面に JSON の内容は表示されているのでコピペでもできそう  

<img src="/images/2019/aws-cloudformation/former_create12.png" />  
<img src="/images/2019/aws-cloudformation/former_create13.png" />  

cloudFormationで権限のあるバケットにちゃんと出力されてた  

<img src="/images/2019/aws-cloudformation/former_create14.png" />  

---

今回は取得したリソースからの再作成までは検証してない  
再作成確認まではしてないからバグが有るのかは確認できてない  

ALB が取得可能リソースになかったのは痛い  
またどうしても AWSサービスの後追いになってしまうのでスピート感はしょうがないとはいえ早くない  

ただ EC2 はスナップショット/AMI、などバックアップで考えた場合の代替手段がある以外の  
今までコマンドで設定値をJSON出力していた系のVPCやセキュリティグループなど一番変更が多いもののバックアップ手段ができたのは嬉しい  

あとは早く正式版リリースされて、  
IAM とか、マネージド・サービス系を増やしてほしいなー  
※あまり積極的に開発されてる感無いしひょっとしたら Terraform とか取り込んで全く別のツールになるかもだけど  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4822237443/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/51KpOuWyhYL._SL160_.jpg" alt="Amazon Web Services 基礎からのネットワーク&サーバー構築 改訂版" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
