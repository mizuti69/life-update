+++
title = "AWS Cloud Watch Logs 導入検証 続き"
tags = ["aws"]
categories = ["SE"]
date = "2019-08-06"
description = "AWS Cloud Watch Logs で取得したログファイルをキーワード監視して通知させる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## 前回からの続き
前回 <i class="fas fa-external-link-alt"></i> [AWS Cloud Watch Logs 導入検証](/2019/07/31/aws-cloudwatchlogs/) にてログを CloudWatch に取り込むまではできた  
次はそれを監視してメール通知するようにする  

## メトリクスフィルタの作成
ログ監視設定を行い、検知した場合 CloudWatch Alerm に飛ばすようにする  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup08.png" />  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup09.png" />  

フィルタパターンに検知したい文言を記載する  
`"error"` と書くと 完全一致でそれ意外を検知してくれないが、  
`?error ?Error ?ERROR` と書けばちょっと手間だけど複数の指定も出来る  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup10.png" />  

## CloudWatch Alerm の設定
メトリクスフィルタにマッチした場合検知するように Alerm を作成  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup11.png" />  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup12.png" />  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup13.png" />  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup14.png" />  

アラームを作成するとメトリクスにちゃんと関連付けされる  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup15.png" />  

## 検知確認
EC2 インスタンス上で適当にアラートログを出してみる  

```
# logger Error
# tail /var/log/messages
Aug  5 20:54:10 localhost ec2-user: Error
```

ほぼほぼ即時通知が来た  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup16.png" />  

ただ何が起きたかはメールだとわかりにくぅ・・・  
`Name` と `Description` で判断するしか無いからちゃんと設定しないとね  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
