+++
title = "AWS CloudWatch Synthetics 検証"
tags = ["aws"]
categories = ["SE"]
date = "2020-02-03"
description = "AWS CloudWatch Synthetics プレビュー版が東京リージョンで使えるようになったので"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## CloudWatch Synthetics
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/about-aws/whats-new/2020/01/amazon-cloudwatch-synthetics-available-13-additional-regions/" data-iframely-url="//cdn.iframe.ly/hC5PGJL?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金
無料利用枠：1 か月あたり 100 回の Canary 実行  
料金：Canary 実行ごと0.0019USD  
あと CloudWatchLogs、 CloudWatchAlerm、APIコール 分とか S3 とかもかかりそう  

## モニタリングの作成  
<i class="fas fa-external-link-alt"></i> [カナリアの作成](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Synthetics_Canaries.html)  

<img src="/images/2020/aws-cloudwatch/synthetics-01.png" />  

### Canaryの作成  
監視を行うための処理（スクリプト）は

* 設計図を使用する  
* スクリプトをアップロードする  
* S3からインポート  

の選択肢がある  

<img src="/images/2020/aws-cloudwatch/synthetics-02.png" />  

とりあえずテンプレートから触ってみる  
設計図を選ぶと

* ハートビートのモニタリング  
* API Canary  
* リンク切れチェッカー   
* GUI ワークフロービルダー  

があるのでとりあえずハートビートのモニタリングをしてみる  

<img src="/images/2020/aws-cloudwatch/synthetics-03.png" />  

ビルダーに URL を指定するわけだけど、きっとこれ認証とかアクセス制御してたら出来ないんだろなぁ  
URLを入力すると自動でスクリプトに反映される  
テンプレートのスクリプトは JS で書かれているので他の記事でも見たけどバックグラウンドは Lambda で動いてる  

```
var synthetics = require('Synthetics');
const log = require('SyntheticsLogger');

const pageLoadBlueprint = async function () {

    // INSERT URL here
    const URL = "https://hogehoge.com/";

    let page = await synthetics.getPage();
    const response = await page.goto(URL, {waitUntil: 'domcontentloaded', timeout: 30000});
    //Wait for page to render.
    //Increase or decrease wait time based on endpoint being monitored.
    await page.waitFor(15000);
    await synthetics.takeScreenshot('loaded', 'loaded');
    let pageTitle = await page.title();
    log.info('Page title: ' + pageTitle);
    if (response.status() !== 200) {
        throw "Failed to load page!";
    }
};

exports.handler = async () => {
    return await pageLoadBlueprint();
};
```

スクリプトを見てみると、応答タイムアウトかな？ `await page.waitFor(15000)` と  
何をもってエラーかどうか判定するための `if (response.status() !== 200) ` がある  
この辺は環境や用途に応じて調整するポイント  

ライブラリは

* Syntheticsライブラリバージョン1.0
* Puppeteer-coreバージョン1.14.0
* Puppeteer-core 1.14.0に一致するChromiumバージョン

で構成されていてLambdaコードは、最大メモリが1 GB、10分後、またはカナリアの頻度値のいずれか短い方でタイムアウトするらしい  

<img src="/images/2020/aws-cloudwatch/synthetics-04.png" />  

実行間隔  
最短でも1分間隔、最大で1時間間隔まで  

<img src="/images/2020/aws-cloudwatch/synthetics-05.png" />  

データ保持  
範囲は1〜600日 カスタム値があるのである程度自由に設定できそう  
ログは CloudWatchLogs に出るっぽい  

<img src="/images/2020/aws-cloudwatch/synthetics-06.png" />  

データ・ストレージ  
テストランデータが出力されるらしい（実際に最後まで手順進めてみたけど何も出力されなかった）  
**この作業は初回しか設定できず、Canary 作成後は確認もできない**  

<img src="/images/2020/aws-cloudwatch/synthetics-07.png" />  

しきい値とアクセス許可  
しきい値は判断項目がスクリプト内にあるのでいいが、IAMロールが新しく作らせてくれない  
しょうがないので S3、 CloudWatchLogs、 CloudWatchメトリクス発行 らへんの権限を持っているロールを事前に作っておく  

### 動作確認  
<img src="/images/2020/aws-cloudwatch/synthetics-08.png" />  

とりあえず作成するとすぐに動き出す  
ちなみに対象のURLはアクセス制限があるのであっさりと失敗になった  

<img src="/images/2020/aws-cloudwatch/synthetics-09.png" />  

なぜ失敗したか、が監視のキモだがちゃんと見れるようになってる  
ただスクリーンショットやHARファイル、ログのプレビューにはめちゃくちゃ時間かかる  

<img src="/images/2020/aws-cloudwatch/synthetics-10.png" />  

メトリクスでは Lambda の実行状況とか  

<img src="/images/2020/aws-cloudwatch/synthetics-11.png" />  

設定では設定内容と実行間隔やしきい値、スクリプトを再修正できる  

### 通知
監視失敗時にアラート通知したい場合は「Thresholds」にてアラームを有効化する必要がある  

<img src="/images/2020/aws-cloudwatch/synthetics-12.png" />  

有効化すると作成した Canary の設定編集からしきい値を有効化できるようになる  

<img src="/images/2020/aws-cloudwatch/synthetics-13.png" />  

有効化すると自動でアラームが作成されるので  
SNSにトピックを発行したり  
AutoScaling を起動したり  
EC2 アクションを実行したりできる  

<img src="/images/2020/aws-cloudwatch/synthetics-14.png" />  

### Lambda構成のカスタマイズ
Canaryで作成した監視用Lambda関数は下記のように登録されているの  
Canaryは外部監視を出来ることが売りだが、VPCエンドポイントとか作って内部監視  
NATGWとか使って固定IP化して一般公開以外のサイト監視にも対応など

結局やってることは Lambda 作って Cloudwatch でアラート監視しましょなので色々できそう  

---
使ってみて思ったことは、 Lambda + CloudWatch で頑張れば実装できる 細かい監視設定を  
マネージドで自動化して作成できますよ！というサービスなんだな  

その分細かい要望や要件に柔軟に対応できるようになっている  
どうせなら Connect とかと連携して電話連絡できるようにしてくれるといいなー  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
