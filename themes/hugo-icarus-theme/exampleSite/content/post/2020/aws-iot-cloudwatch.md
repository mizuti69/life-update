+++
title = "Raspbian のリソース監視を IoT Core の MQTT で CLoudWatch 連携"
tags = ["AWS","IoT"]
categories = ["SE"]
date = "2020-09-07"
description = "デバイスのリソース監視を IoT Core 連携して CloudWatchカスタムメトリクス監視する"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS IoT Core
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/iot-core/" data-iframely-url="//cdn.iframe.ly/gTykIvS?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### MQTT を使った IoT Core連携
IoT Core はデバイスとAWS環境を関連付け、MQTT または HTTP 通信を行うことで簡単なデータ連携が出来る  

MQTTとは？
メッセージ指向ミドルウェアのアプリケーション層で使用される、TCP/IPによるPub/Sub型データ配信モデルの軽量なデータ配信プロトコルである  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://ja.wikipedia.org/wiki/MQ_Telemetry_Transport" data-iframely-url="//cdn.iframe.ly/RUmRgo1"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

デバイス側から AWS の IoT Core エンドポイントに対して MQTT/HTTP 通信するには SDK が提供されているが、  
MQTT送るだけであれば OS コマンドでも可能  
システムリソースを取得するのであれば OS コマンド使ったほうが相性いいし、シェルスクリプト書くよ  

事前に IoT Core へのデバイス登録を行い証明書を発行、デバイスに配置しておく  


MQTTクライアントツールのインストール  

```
# apt install mosquitto-clients
```

スクリプト作成  
送るデータにグラフ用データを一緒に送ることで、後述する AWS側の MQTT トピックルールを一つだけににさせる  
とりあえずのスクリプトなので雑  

```
#!/bin/bash

## Get hostname
HOSTNAME=$(hostname)

## AWS IoT Core environments
AWS_IOT_END="{{ aws_iot_endpoint }}"
AWS_IOT_TOPIC="{{ aws_iot_topic }}"
AWS_IOT_DEVCEID="{{ aws_iot_name }}"
CERT_DIR="{{ aws_iot_certdir }}"
CERT_CA=$(find "$CERT_DIR" -type f -name "*root*")
CERT_CE=$(find "$CERT_DIR" -type f -name "*certificate.pem*")
CERT_KEY=$(find "$CERT_DIR" -type f -name "*private.pem*")


## Get status
GET_DISK=$(df -h |awk '/\/dev\/root/{sub("\%", " ", $5); print $5 }')
GET_CPU=$(awk -v oldidle=$(awk '/cpu / {print $5}' /proc/stat; sleep 1) '/cpu / {perc=($5-oldidle)/100 ; printf "%s", perc}' /proc/stat)
GET_CPU_TEMP=$(vcgencmd measure_temp |awk -F\= '{sub("'\''C", " ", $2); print $2}')
GET_MEM=$(free --mega |awk '/Mem/{print $3}')

## Put aws
put_data(){
  /usr/bin/mosquitto_pub --cafile "$CERT_CA" --cert "$CERT_CE" --key "$CERT_KEY" -h "$AWS_IOT_END" -p 8883 -q 1 -t "$AWS_IOT_TOPIC" -i "$AWS_IOT_DEVCEID" --tls-version tlsv1.2 -m "$msg" -d
  logger -t batch "aws iot put status $?: message: $msg"
}


## Message disc(/) usage
msg="{
    \"name\": \"disk usage / ($HOSTNAME)\",
    \"unit\": \"Percent\",
    \"value\": \"$GET_DISK\"
    }"
put_data

## Message cpu usage
msg="{
    \"name\": \"cpu usage ($HOSTNAME)\",
    \"unit\": \"Percent\",
    \"value\": \"$GET_CPU\"
    }"
put_data

## Message cpu temperature(℃)
msg="{
    \"name\": \"cpu temperature ($HOSTNAME)\",
    \"unit\": \"None\",
    \"value\": \"$GET_CPU_TEMP\"
    }"
put_data

## Message memory usage
msg="{
    \"name\": \"memory usage ($HOSTNAME)\",
    \"unit\": \"Megabytes\",
    \"value\": \"$GET_MEM\"
    }"
put_data
```

> CLoudWatch の Metric に利用できるパラメータは  
> Seconds, Microseconds, Milliseconds, Bytes, Kilobytes, Megabytes, Gigabytes, Terabytes, Bits, Kilobits, Megabits, Gigabits, Terabits, Percent, Count, Bytes/Second, Kilobytes/Second, Megabytes/Second, Gigabytes/Second, Terabytes/Second, Bits/Second, Kilobits/Second, Megabits/Second, Gigabits/Second, Terabits/Second, Count/Second, None  
> のみ  

### AWS IoT Core Action
AWS IoT Core 側に MQTT受信時のアクションとトピックを作成  

**Act** の ルーツにて MQTT の送信先 Topic を指定する  
トピック名以外クエリに特別なことはしない  

<img src="/images/2020/aws/aws-iot/iot-cw-01.png" />  

アクション先に 「CLoudWatchメトリクスにメッセージデータを送信する」を選択する  
これをすればわざわざ Lambda を挟んでカスタムメトリクスに構文をフォーマットしてーとやらなくていい  

「メトリクス名」、「単位」、「値」はデバイスのスクリプトから渡すデータ内の値を参照させる  
「メトリクスの名前空間」はカスタムメトリクスのグループ名になるので今回は固定でいい  

<img src="/images/2020/aws/aws-iot/iot-cw-02.png" />  

一応エラー時に SNS トピックをアクション先に指定しておく  
最初 CloudWatch との連携部分でエラー内容が分からなくて苦労したので素直に確認できるようにしておく  
※AWSドキュメントが不親切なのが悪い()  

<img src="/images/2020/aws/aws-iot/iot-cw-03.png" />  

### Topicサブスクライブ用のセキュリティポリシー設定
デバイス側には AWS Credential 情報は不要で証明書だけでいいが、
逆に AWS IoT Core 側にて IAM 権限をデバイス単位、証明書単位、あるいはグループ単位で権限付して上げる必要がある  
これをしないとエラーで疎通できない  

<img src="/images/2020/aws/aws-iot/iot-cw-04.png" />  

### 監視データの送信
作成したスクリプトを cron 等で１分置きとかに実行するようにして
CloudWatch 側にちゃんとデータが入ってくるか確認する  

上手く行けば下記のようにいい感じに CLoudWatch 上でグラフィカルに監視できる  

<img src="/images/2020/aws/aws-iot/iot-cw-05.png" />  

---

AWS IoT Core の MQTT アクションルールによる CloudWatch 連携のやりかたが日本語の記事少なかったのでちょっと苦労した  
Lambda に入れて～みたいのはあったけど  
他にもログを連携しようと思ったら Fluentd か 同様に MQTT 経由で CloudWatchLogs に連携できる  

IoT デバイスを CloudWatch で監視しようとすると自動的にカスタムメトリクスになるため追加で費用がかかる  
IoT デバイスの利用を考えたときその利用ケースに応じた端末数に比例して、監視や運用、前回のSSMもそうだけど追加コストが膨大に発生する  

現状AWS側しか利用してないのでわからないけど、Azure とか他サービスはどうなんだろう？  
ちょっと真面目に考えると IoT デバイスの運用って下手なクラウドサーバ運用より費用掛かりそうなので  
プラットフォーム選定は大事  

{{< rawhtml >}} 
<div style="border: dashed 1px #ccc;">
<a href="https://www.amazon.co.jp/%E6%94%B9%E8%A8%82%E6%96%B0%E7%89%88-IoT%E3%82%A8%E3%83%B3%E3%82%B8%E3%83%8B%E3%82%A2%E9%A4%8A%E6%88%90%E8%AA%AD%E6%9C%AC-Software-Design-plus%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA/dp/4297106906/ref=as_li_ss_il?_encoding=UTF8&qid=1597922218&sr=8-7&linkCode=li2&tag=sinokyoufu-22&linkId=39ff595ea733eeb42572ae5eacf565f2&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4297106906&Format=_SL160_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li2&o=9&a=4297106906" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}
