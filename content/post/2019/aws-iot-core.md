+++
title = "Raspbisn と AWS IoT Core を連携してみる"
tags = ["AWS","IoT"]
categories = ["SE"]
date = "2019-11-21"
description = "IoT 仮想入門。機器から受け取ったデータを AWS IoT Core と連携してプッシュ通知までしてみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS IoT  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/iot-core/" data-iframely-url="//cdn.iframe.ly/gTykIvS?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

> AWS IoT Core ではデバイスをクラウドや他のデバイスに簡単に接続できます。AWS IoT Core では、断続的な接続を許容し、デバイスのコードフットプリントを削減し、必要なネットワーク帯域幅を削減するよう特別に設計された HTTP、WebSocket、MQTT といった軽量の通信プロトコルをサポートしています。また、AWS IoT Core では他の業界標準およびカスタムプロトコルをサポートしており、別のプロトコルを使用しているデバイス間でも相互に通信できます。  

### 料金  
<i class="fas fa-external-link-alt"></i> [AWS IoT Core 料金](https://aws.amazon.com/jp/iot-core/pricing/?nc=sn&loc=4)  

接続料金: 0.096USD (接続 100 万分あたり)  
メッセージ 10 億件まで: 1.20USD (メッセージ 100 万件あたり)  
トリガーされたルール: 0.18USD (トリガーされたルール 100 万件あたり/実行されたアクション 100 万件あたり)  
実行されたアクション: 0.18USD (トリガーされたルール 100 万件あたり/実行されたアクション 100 万件あたり)  

AWS IoT Core と接続して、メッセージを送り、Lambda 等実行すればそれぞれに料金が発生するイメージか  
各項目とも100万件あたり～とあるように小規模で運用する場合はほぼ数ドルで済む  

## IoT Core の開始  
<i class="fas fa-external-link-alt"></i> [AWS IoT の使用開始](https://docs.aws.amazon.com/ja_jp/iot/latest/developerguide/iot-gs.html)  

### モノの登録  
<img src="/images/2019/aws-iot/setup-01.png" />  
<img src="/images/2019/aws-iot/setup-02.png" />  

単一のモノを作成します  

<img src="/images/2019/aws-iot/setup-03.png" />  
<img src="/images/2019/aws-iot/setup-04.png" />  

モノと AWS IoT Core を接続させる認証鍵を作成します  

<img src="/images/2019/aws-iot/setup-05.png" />  

作成した有効化し証明書はダウンロードしておく  
ルートCA「RSA 2048 ビットキー: Amazon ルート CA 1」のみ下記からダウンロード  
<i class="fas fa-external-link-alt"></i> [サーバー認証 - サーバー認証用の CA 証明書](https://docs.aws.amazon.com/ja_jp/iot/latest/developerguide/server-authentication.html)  

<img src="/images/2019/aws-iot/setup-06.png" />  

モノにポリシー、IAMのようなアクセス権限を設定する  

<img src="/images/2019/aws-iot/setup-07.png" />  

作成してないので無く、ここから作成出来ないのでサイドメニューの「安全性」から作成する  

<img src="/images/2019/aws-iot/setup-08.png" />  
<img src="/images/2019/aws-iot/setup-09.png" />  

テストなのでかなり自由な権限を与える  

<img src="/images/2019/aws-iot/setup-10.png" />  

作成したポリシーはモノに設定した証明書と紐付ける  

<img src="/images/2019/aws-iot/setup-11.png" />  
<img src="/images/2019/aws-iot/setup-12.png" />  

これで AWS IoT Core 側の初期設定は完了  

## デバイスの設定
<i class="fas fa-external-link-alt"></i> [Raspberry Pi で AWS IoT SDKを使用する](https://docs.aws.amazon.com/ja_jp/iot/latest/developerguide/sdk-tutorials.html)

### SDKのインストール
<i class="fas fa-external-link-alt"></i> [AWS IoT SDK - AWS IoT Device SDK for Python](https://docs.aws.amazon.com/ja_jp/iot/latest/developerguide/iot-sdks.html#iot-python-sdk)  
SDKは C か c++ か Java か Javascript か Python がある JS版を入れてみる  

```
$ sudo apt-get install -y nodejs npm
$ git clone https://github.com/aws/aws-iot-device-sdk-python.git  
$ cd aws-iot-device-sdk-js/
$ npm install
```

### サンプルアプリケーションの実行
IoT Core と通信するための 必要情報を確認する  

* AWS リージョン  
  ap-northeast-1  

* クライアントID  
  IoT モノの名前と同じ  

* ユーザーのプライベートキー  
  `/home/pi/aws-iot-device-sdk-js/certs/private.pem.key`

* 証明書  
  `/home/pi/aws-iot-device-sdk-js/certs/certificate.pem.crt`  

* ルートCA  
  `/home/pi/aws-iot-device-sdk-js/certs/Amazon-root-CA-1.pem` 

* AWS IoT エンドポイント  
  <img src="/images/2019/aws-iot/setup-13.png" />  

* AWS IoT メッセージブローカーがリッスンするポート  
  8883  

* IoT モノの名前  
  AWS IoT に登録したときに使用した名前 

AWS SDK に用意されているサンプルを実行してみる  

```
$ cd aws-iot-device-sdk-js/examples
$ node device-example -k "../certs/private.pem.key" -c "../certs/certificate.pem.crt" -i "vIoT-study" -a "../certs/Amazon-root-CA-1.pem" -H "************.iot.ap-northeast-1.amazonaws.com" -p 8883 -T "vIoT-study" --test-mode 1

connect
```

デバイスは1台しか無いので相互メッセージの通信は確認できないが、  
コンソール上で確認してみると接続が出来ていることを確認できる  

<img src="/images/2019/aws-iot/setup-14.png" />  

エッジコンピュータのゲートウェイ側はこんな感じでつながってるのね  

### AWS IoT Core にメッセージを送る  
AWS IoT Core 側で MQTT トピックを作成  

<img src="/images/2019/aws-iot/setup-15.png" />  

適当なプッシュを作り  

```
// AWS IoT SDK
const awsIot = require('../aws-iot-device-sdk-js')

// AWS IoT Core 認証情報
const device = awsIot.device({
        region: 'ap-northeast-1',
        clientId: 'vIoT-study',
        keyPath: 'certs/private.pem.key',
        certPath: 'certs/certificate.pem.crt',
        caPath: 'certs/Amazon-root-CA-1.pem',
        host: '**************.iot.ap-northeast-1.amazonaws.com',
});

// 接続
device.on('connect', function() {
        console.log('connect');
        setInterval( function() {
                device.publish('study_topic1', JSON.stringify({ value: 'hogehoge' }));
        }, 5000);
});
```

実行すると  

```
$ node push.js
```

トピック側にメッセージが来ていることを確認できる  

<img src="/images/2019/aws-iot/setup-16.png" />  

### ルールの作成
<i class="fas fa-external-link-alt"></i> [AWS IoT - AWS Lambda ルールを作成する](https://docs.aws.amazon.com/ja_jp/iot/latest/developerguide/iot-lambda-rule.html)  
メッセージを送った際に AWS IoT Core で Lambda をキックさせる  
Lambda関数を作成し  

<img src="/images/2019/aws-iot/setup-17.png" />  

ルールを作成する  

<img src="/images/2019/aws-iot/setup-18.png" />  
<img src="/images/2019/aws-iot/setup-19.png" />  

ルールのクエリーステートメントはどの Topic に対してこのルールを適用するかを定義している  
IoT 機器毎に処理を分けたい場合は Topic 名で分ける  
Topic 名で分けるだけなら SQL である必要が無い気がするので、細かい条件処理をすることもできるってことなのかな？  

<img src="/images/2019/aws-iot/setup-20.png" />  
<img src="/images/2019/aws-iot/setup-21.png" />  

ルールによってアクションできるサービスは現在下記  

* DynamoDB テーブルにメッセージを挿入する  
* DynamoDBv2 データベーステーブルの複数列にメッセージを分割する  
* Lambda関数を呼び出す  
* SNS通知  
* SQSキューにメッセージを送る  
* Kinesis ストリームにメッセージを送信  
* IoT トピックにメッセージを再パブリッシュ  
* S3 バケットにメッセージを格納  
* Kinesis Firehose ストリームメッセージを送信  
* CloudWatchにメッセージを送信  
* CloudWatchアラームの状態を変更  
* Elasticsearch にメッセージを送信  
* Salseforce IoT 入力ストリームにメッセージを送信  
* IoT Analytics にメッセージ送信  
* IoT Events 入力いメッセージ送信  
* Step Function ステートマシンの実行を開始する  

<img src="/images/2019/aws-iot/setup-22.png" />  
<img src="/images/2019/aws-iot/setup-23.png" />  

テストなのでエラー処理は入れていない  
以上でルールを作成完了  

### AWS IoT + Lambda 連携確認  
連携だけであれば AWS IoT の画面だけでテストできる  

lambda コード  
受け取ったメッセージを　Googlehungout Chat に送る  

```
// modules
const request = require('request');

// function
exports.handler = function(event, context) {
    let message = event.message;
    let options = {
        url: process.env['hungoutChat'],
        method: 'POST',
        headers: 'Content-Type : application/json',
        json: {
           "text": message
        }
    };
    console.log(message);
    
    request(options, function(error, response, body){
        console.log(body);
    });
};
```

アクションにて設定した Topic に対して メッセージをサブスクライブ  

<img src="/images/2019/aws-iot/setup-24.png" />  

無事 Lamdba も動いて 送信したメッセージ内容が Google Hungout Chat に通知された  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392436/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/51I4r5%2BxaaL._SL160_.jpg" alt="IoTの基本・仕組み・重要事項が全部わかる教科書" style="border: none;" /></a>
</div>
{{< /rawhtml >}}