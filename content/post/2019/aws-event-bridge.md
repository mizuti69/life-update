+++
title = "AWS Cloud Watch Event から AWS Event Bridge への移行"
tags = ["aws"]
categories = ["SE"]
date = "2019-12-19"
description = "AWS Cloud Watch Event と Event Bridge は何が違うのか検証してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Amazon EventBridge  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/eventbridge/" data-iframely-url="//cdn.iframe.ly/hbOS8Aw?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

### 料金
AWS のサービスイベント 	無料  
カスタムイベント 	パブリッシュされたカスタムイベント 100 万件あたり 1.00 USD  
サードパーティー (SaaS) のイベント	パブリッシュされたイベント 100 万件あたり 1.00 USD  
クロスアカウントイベント	送信されたクロスアカウントイベント 100 万件あたり 1.00 USD  

CloudWatch Event と同様の金額テーブル  

## Event Bridge の概要  
<i class="fas fa-external-link-alt"></i> [Amazon EventBridge とは?](https://docs.aws.amazon.com/ja_jp/eventbridge/latest/userguide/what-is-amazon-eventbridge.html)  

> EventBridge は、これまで Amazon CloudWatch Events と呼ばれていました。SaaS パートナーやお客様独自のアプリケーションからイベントを受信できる新しい機能が含まれています。既存の CloudWatch イベント ユーザーは、新しい EventBridge コンソールと CloudWatch イベント コンソールで、既存のデフォルトのバス、ルール、イベントにアクセスできます。EventBridge は同じ CloudWatch イベント API を使用するため、既存の CloudWatch イベント API の使用状況に変更は生じません。  

ターゲットサービス  

* Lambda 関数
* Amazon EC2 インスタンス
* Amazon Kinesis Data Streams のストリーム
* Amazon Kinesis Data Firehose の配信ストリーム
* Amazon CloudWatch Logs のロググループ
* Amazon eventbridge タスク
* Systems Manager コマンドを実行
* Systems Manager オートメーション
* AWS Batch ジョブ
* AWS Step Functions ステートマシン
* AWS CodePipeline のパイプライン
* AWS CodeBuild プロジェクト
* Amazon Inspector の評価テンプレート
* Amazon SNS のトピック
* Amazon SQS キュー
* 組み込みターゲット: EC2 CreateSnapshot API call、EC2 RebootInstances API call、EC2 StopInstances API call、および EC2 TerminateInstances API call
* 別の AWS アカウントのデフォルトのイベントバス

### パートナーイベントソース
CloudWatch Event には無かった SaaS 製品との連携  
パートナーイベントソースは、AWS アカウントにイベントを送信するために AWS パートナーによって使用されます。  
これらのイベントを受信するには、お客様はイベントバスをパートナーイベントソースに関連付ける必要があります。  

<img src="/images/2019/aws-eventbridge/eventbridge-01.png" />  

### ルールのタグ管理
イベントルールにタグが付けられるようになった  

<img src="/images/2019/aws-eventbridge/eventbridge-02.png" />  

### イベントバスという考え方  
CloudWatch Event の場合既存のAWSリソース内から選んで他AWSサービスと連携するのみだったが、  
インプット元を自作のアプリケーション等から連携できるようになったため、カスタムアプリ用のイベントバスを作れるようになった  
アカウントには、AWS サービスからイベントを受け取るデフォルトのイベントバスが 1 つあります  

<img src="/images/2019/aws-eventbridge/eventbridge-03.png" />  

### ルールの連携データ細分化  
イベント発生時のターゲット先を複数指定できたり、カスタム JSON 、イベント内容の一部だけ連携等できる  
Lambda でグリグリ JSON を加工して　Slack とかにプッシュしてたけど、シンプルにできそう  

<img src="/images/2019/aws-eventbridge/eventbridge-04.png" />  

これらは CLoudWatch Event で作成していたイベントルールだったがちゃんと変更できるようになっている  

### Schema registry  
こういうサービス連携系、Lambda連携系のときによく困るのが「で、どういうデータ(JSON)が送られてくるんだ？」という点  
それをオープンにして開発や連携をしやすくしてくれるもの  

<img src="/images/2019/aws-eventbridge/eventbridge-05.png" />  

ただまだプレビュー版？のようでふーんくらいに参考にする程度でいいのかも  

---

CloudWatch Event からの移行において特にやることはなく  
CloudWatch Event では出なかった、こういう事ができればなーが出来るようになっている  
サードパーティとの連携が出来るようになったため美しくない Lambda や OS 上のバッチを作らなくて良くなるのはいいなぁ  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392568/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61N%2BYkPAOtL._SL160_.jpg" alt="Amazon Web Services 業務システム設計・移行ガイド (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
