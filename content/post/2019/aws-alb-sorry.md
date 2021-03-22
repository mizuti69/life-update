+++
title = "ALB Sorry対応"
tags = ["aws"]
categories = ["se"]
date = "2019-10-09"
description = "ALBの固定レスポンス応答を利用して自動Sorry表示を考える"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## ALBの作成
ALBの初期作成についてはここでは割愛する  
ベーストラフィックも特に理由がなければ固定レスポンス応答にして Sorry画面を返すようにしておく  

<img src="/images/2019/aws-alb-sorry/elb_alb_sorry01.png" />  


固定レスポンスで設定できるステータスコードは「2XX」「4XX」「5XX」系  
記述できるコンテンツタイプは `text/plain` `text/css` `text/html` `application/javascript` `application/json`  
記載できる文字数は 1024 文字まで、わりとリッチなコンテンツも設定できそう  

<img src="/images/2019/aws-alb-sorry/elb_alb_sorry02.png" />  

次に通常時用のルールを設定する  
ALB 単体の機能では自動で固定レスポンスのルールを障害時に返すようには出来ないのでルールだけ設定しておく  

<img src="/images/2019/aws-alb-sorry/elb_alb_sorry03.png" />  

ALB のURLにアクセスして コンテンツが表示される事を確認  
https://***********************.ap-northeast-1.elb.amazonaws.com/index.html  

## ダウン確認
### CloudWatchアラームの設定  
正常系ホストの数が０になった時にアラート検知するようにアラームを作成  

<img src="/images/2019/aws-alb-sorry/elb_alb_sorry04.png" />  
<img src="/images/2019/aws-alb-sorry/elb_alb_sorry05.png" />  

とりあえず１分間の平均値が０になった検知を５分連続で検知したらアラートとするよう設定  
API経由でステータスを確認  

```
# aws cloudwatch describe-alarms --alarm-names "*******************************"
{
    "MetricAlarms": [
        {
            "EvaluationPeriods": 5,
            "AlarmArn": "arn:aws:cloudwatch:ap-northeast-1:**********:alarm:awsapplicationelb-targetgroup-alb-study-top-group-***********-High-",
            "StateUpdatedTimestamp": "2019-07-12T10:11:35.951Z",
            "AlarmConfigurationUpdatedTimestamp": "2019-07-12T10:14:55.919Z",
            "ComparisonOperator": "LessThanOrEqualToThreshold",
            "AlarmActions": [
                "arn:aws:sns:ap-northeast-1:****************:config-alert-topic"
            ],
            "Namespace": "AWS/ApplicationELB",
            "AlarmDescription": "Created from EC2 ",
            "StateReasonData": "{\"version\":\"1.0\",\"queryDate\":\"2019-07-12T10:11:35.947+0000\",\"startDate\":\"2019-07-12T10:06:00.000+0000\",\"statistic\":\"Average\",\"period\":300,\"recentDatapoints\":[0.0],\"threshold\":0.0}",
            "Period": 60,
            "StateValue": "ALARM",
            "Threshold": 0.0,
            "AlarmName": "awsapplicationelb-targetgroup-alb-study-top-group-****************-High-",
            "Dimensions": [
                {
                    "Name": "LoadBalancer",
                    "Value": "app/alb-study/*****************"
                },
                {
                    "Name": "TargetGroup",
                    "Value": "targetgroup/alb-study-top-group/*****************"
                }
            ],
            "DatapointsToAlarm": 5,
            "Statistic": "Average",
            "StateReason": "Threshold Crossed: 1 out of the last 1 datapoints [0.0 (12/07/19 10:06:00)] was less than or equal to the threshold (0.0) (minimum 1 datapoint for OK -> ALARM transition).",
            "InsufficientDataActions": [],
            "OKActions": [],
            "ActionsEnabled": true,
            "MetricName": "HealthyHostCount"
        }
    ]
}
```

`"StateValue": "OK",` になっている  

### Sorry切り替え
ターゲットグループのメンバーホストを停止(apacheの停止)しアラートを発生させる  
CloudWatchのアラーム状態を確認  

```
# aws cloudwatch describe-alarms --alarm-names "awsapplicationelb-targetgroup-alb-study-top-group-************-High-" |jq '.MetricAlarms[].StateValue'
"ALARM"
```

CloudWatch のアラームを検知したら ALB に設定した固定レスポンス応答の優先順位を最上位に変更する  
今は Bad Gateway になっているが  

<img src="/images/2019/aws-alb-sorry/elb_alb_sorry06.png" />  

固定レスポンスルールのプライオリティ1にする  

```
# aws elbv2 set-rule-priorities --rule-priorities RuleArn=arn:aws:elasticloadbalancing:ap-northeast-1:********:listener-rule/app/alb-study/******/******/******,Priority=1
ortheast-1:********:listener-rule/app/alb-study/********/********/********,Priority=1
{
    "Rules": [
        {
            "Priority": "1",
            "Conditions": [
                {
                    "Field": "path-pattern",
                    "Values": [
                        "*"
                    ]
                }
            ],
            "RuleArn": "arn:aws:elasticloadbalancing:ap-northeast-1:********:listener-rule/app/alb-study/********/********/********",
            "IsDefault": false,
            "Actions": [
                {
                    "Type": "fixed-response",
                    "Order": 1,
                    "FixedResponseConfig": {
                        "ContentType": "text/html",
                        "MessageBody": "<!DOCTYPE html>\n<html>\n<head>\n<title>Untitled-1.md</title>\n<meta http-equiv=\"Content-type\" content=\"text/html;charset=UTF-8\">\n\n</head>\n<body>\n<h1>メンテナンス</h1>\n<p>只今メンテナンス中になります。しばらく時間をおいてからアクセスしてください</p>\n\n</body>\n</html>\n",
                        "StatusCode": "503"
                    }
                }
            ]
        }
    ]
}
```

表示が切り替わっていることを確認できた  

<img src="/images/2019/aws-alb-sorry/elb_alb_sorry07.png" />  

元に戻す場合は CloudWatch のアラーム状況を確認して正常ルールのプライオリティを上げればいい  

```
# aws elbv2 set-rule-priorities --rule-priorities RuleArn=arn:aws:elasticloadbalancing:ap-northeast-1:********:listener-rule/app/alb-study/********/********/*******,Priority=1
```

あとは Lamdba なり スクリプトなりで用意すればいい  

## メンテナンス時対応
メンテナンス時、一般ユーザーにはメンテナンス画面を表示し、作業者には画面を見せたい場合  
メンテナンス用サブドメインを用意して、通常ドメイン時は固定レスポンス応答設定にし、メンテナンスサブドメインは見せるようにすればいける？  
アクセス制御は出来ないけど  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
