+++
title = "CapitalOne への SSRF 攻撃からみる、AWS のセキュリティ監視検討"
tags = ["aws"]
categories = ["SE"]
date = "2019-08-09"
description = "CapitalOne へ SSRF攻撃が行われ、 AWS 上の情報が盗まれた事件から、AWS利用にあたり追加でできるセキュリティ監視を考える"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## はじめに
本事件について簡単に  

<iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Fpiyolog.hatenadiary.jp%2Fentry%2F2019%2F08%2F06%2F062154" style="border: 0; width: 100%; height: 190px;" allowfullscreen scrolling="no"></iframe>  

また参考までに  

<div style="left: 0; width: 100%; height: 0; position: relative; padding-bottom: 56.25%; padding-top: 38px;"><iframe src="https://www.slideshare.net/slideshow/embed_code/key/mA7M6ENpHDNjFF" style="border: 0; top: 0; left: 0; width: 100%; height: 100%; position: absolute;" allowfullscreen scrolling="no" allow="encrypted-media"></iframe></div>  

AWSの脆弱な仕組みが突かれて起きた問題、というわけではないようだが  
IAM の設定やベストプラクティスの理解、一体どこでどのように管理対応するのが良いのか出来すぎる事による複雑性  
AWSを利用する上では必ずついてまわるめ出来ること、できそうな事を考えておく  

## GuardDuty
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/guardduty/" data-iframely-url="//cdn.iframe.ly/RiyvF7e?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  
一番簡単で効果的な不正トラフィックの監視を行ってくれるサービス  

### 料金
CloudTrail イベント分析 – GuardDuty は、CloudTrail イベント を継続的に分析し、AWS のアカウントとインフラストラクチャのすべてのアクセスと動作をモニタリングします。CloudTrail 分析は、毎月 1,000,000 件のイベントごとに課金され、プロ評価されます。  
VPC フローログと DNS ログの分析 – GuardDuty は、VPC フローログおよび DNS のリクエストと応答を継続的に分析し、AWS のアカウントとワークロードで悪意のある動作、不正な動作、または予期しない動作を識別します。フローログと DNS ログの分析は、1 か月あたりのギガバイト (GB) 単位で課金されます。フローログと DNS ログの分析には、段階的な従量制割引が適用されます。  

通信量とイベント数の従量課金、通信量ではなく通信ログの量なので注意  
規模によるがこそこの金額がかかりそう  

### GuardDutyの利用  
サービス画面で有効可を行うだけ  

<img src="/images/2019/aws-security/guardduty_setup01.png" />  

有効になると EC2 に直接通信するようなサービスやポートがあった場合通知してくれる  
ただ遡及して検知はしないため、利用する場合はなるべく早くから有効化しておいてほうがいい  

<img src="/images/2019/aws-security/guardduty_setup02.png" />  

## AWS Config
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/config/" data-iframely-url="//cdn.iframe.ly/fZL9CwP?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  
AWS上の設定変更、ステータス変更、コンプライアンス対応状況を監視、通知してくれる  

### 料金
利用するConfigのルール数と監査する対象数と検知数での従量課金となる  
2019年8月に金額計算式の改定が行われ、複雑になっているが従来より安くなっている（らしい）
追加で S3 料金も発生する  

### Configの利用
<img src="/images/2019/aws-security/config_create01.png" />  

Configのログを出力するバケットとロールを作成  

<img src="/images/2019/aws-security/config_create02.png" />  
<img src="/images/2019/aws-security/config_create03.png" />  

ルールの指定  
Configのルールは作成時点で52ルールある  
<i class="fas fa-external-link-alt"></i> [AWS Config マネージドルールのリスト](https://docs.aws.amazon.com/ja_jp/config/latest/developerguide/managed-rules-by-aws-config.html)  
マニュアルを見ながら必要なものだけを指定し、状況を見て必要なら追加するようにしたほうが良さそう  
とりあえず以下のルールを指定してみる  

* s3-bucket-policy-grantee-check  
* restricted-ssh  
* elb-acm-certificate-required  
* iam-root-access-key-check  
* cloudtrail-enabled  
* eip-attached  
* guardduty-enabled-centralized  
* s3-bucket-public-write-prohibited  
* s3-bucket-public-read-prohibited  
* acm-certificate-expiration-check  
* ec2-volume-inuse-check  
* rds-instance-public-access-check  
* ebs-optimized-instance  

ルールを指定し Config の設定を完了すると完了  
初回の解析時は結構時間がかかる  

<img src="/images/2019/aws-security/config_create04.png" />  

解析が終わると問題があった場合、どこに問題があったか教えてくれる  

## IAM のベストプラクティス
<i class="fas fa-external-link-alt"></i> [AWS IAM のベストプラクティス](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/best-practices.html)  

今読んでも理解しきれないけど、よく読んでおく  

## マネージドコンソールへのログインを監視  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/cloudtrail/" data-iframely-url="//cdn.iframe.ly/KYYC9WK?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  
CloudTrail + CloudWatch を利用して AWS マネージドコンソールにログインしたら通知するようにする  

### CloudTrail 証跡の作成
しておく  

### CloudWatch イベントの作成  
**AWSの認証イベントは us-east-1 で記録されるため、CloudWatch Event、SNS も同リージョンに設定する必要がある**  

<img src="/images/2019/aws-security/cloudwatch-signin01.png" />  

設定後、ログインするとメール通知がくる  
あとは Lambda 使ってもいいし  

### CloudWatch Logs で監視する
これはリージョンの縛りはない  
CloudTrailの証跡から CloudWatch Logs にログを連携設定し  

<img src="/images/2019/aws-security/cloudwatch-signin02.png" />  

下記を参考にメトリクスフィルタを作成しアラームを設定する  
<i class="fas fa-external-link-alt"></i> [CloudTrail イベント用の CloudWatch アラームの作成: 追加の例](https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/cloudwatch-alarms-for-cloudtrail-additional-examples.html)  

<img src="/images/2019/aws-security/cloudwatch-signin03.png" />  

メトリクスフィルタを工夫すればログイン失敗情報、API認証失敗などを通知することも出来る  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
