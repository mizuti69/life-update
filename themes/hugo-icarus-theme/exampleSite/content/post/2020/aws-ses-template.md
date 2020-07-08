+++
title = "SES でメール送る時に SES Template を使ってみる"
tags = ["aws"]
categories = ["SE"]
date = "2020-07-08"
description = "AWS Lambda で SES からメールを送る場合に SES Template を使ってみたけど止めたほうが良いかもしれない"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## SES Template
[Amazon SES API を使用してパーソナライズされた E メールを送信する](https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/send-personalized-email-api.html)  

SES Template は コンソール上で操作は出来ない  
API のみ提供されているため色々と使いにくい  

API へのキックは AWS CLI か AWS SDK での操作が出来ますが、ここででは SDK for Javascript でやっていく  

### Template の作成
SES からメールを送信する際に、Template を指定することで、宛先毎にカスタマイズされたメール文面を送ったり  
Template 化されたあメール文面を処理に合わせて動的に変更、指定することが出来る  

SES Template の管理は API 経由でのみ提供されているため  
ドキュメントに沿って簡単なプログラムを作っておく  

SES の API は SES、SESV2 があるが Template が操作できるのは SES の方  
（将来 SES Template 自体無くなるか別出しされそう・・・）  

[AWS SDK for Javascript - Class: AWS SES createTemplate](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SES.html#createTemplate-property)  

```
const AWS = require('aws-sdk');
const ses = new AWS.SES({region: 'us-east-1'});
const params = {
    Template: {
      TemplateName: 'SESTemplateName',
      SubjectPart: 'Mail Subject',
      TextPart: 'Mail Body \r\nHellow world'
    }
};
ses.createTemplate(params, function(err, data) {
    if (err) console.log(err, err.stack);
    else     console.log(data);
});
```

モジュールで呼び出す SES のクラスには SES を作成したリージョンを指定しておく必要がある  
Template では「件名」、「本文」をテンプレート化することが出来る  
本文における改行をしたい場合は `\r\n` で改行できる  
また変数を指定することもでき、変数宣言をしておくと後述する メール送信処理の際に代入させることができる  

```
TextPart: 'Mail Body \r\n{{ vars }}'
```

### Template の更新  
[AWS SDK for Javascript - Class: AWS SES updateTemplate](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SES.html#updateTemplate-property)  
Template を更新したい場合は下記  

```
const AWS = require('aws-sdk');
const ses = new AWS.SES({region: 'us-east-1'});
const params = {
    Template: {
      TemplateName: 'SESTemplateName',
      SubjectPart: 'Mail Subject',
      TextPart: 'Mail Body \r\nHellow world'
    }
};
ses.updateTemplate(params, function(err, data) {
  if (err) console.log(err, err.stack);
  else     console.log(data);
});
```

他削除も同様に SDK の Calss を参照して処理を記述する  

## SES メール送信時の Template 呼び出し
[AWS SDK for Javascript - Class: AWS SESV2 sendEmail](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SESV2.html#sendEmail-property)  
SES で Template を利用したメール送信  

```
const AWS = require('aws-sdk');
const ses = new AWS.SESV2({region: 'us-east-1'});
const sesmsg =  {
    Content: {
        Template: {
            TemplateArn: 'arn:aws:ses:us-east-1:***********:template/SESTEmplateName',
            TemplateData: "{\"vars\":\"Hellow world\"}"
        }
    },
    Destination: {
        BccAddresses: mail_bcc,
        ToAddresses: mail_to
    },
    FromEmailAddress: mail_from
};
ses.sendEmail(sesmsg, function(err, data) {
    if (err) console.log(err, err.stack);
    else     console.log(data); 
});
```

Template の部分で 使いたい Temolate を呼び出すだけ  
Template ARN は **コンソール上も表示されないし、 API 上も確認する方法が無いが、** 上記のように TemplateName を組み合わせる事で指定可能  
Template 変数への代入もここで指定できるが 指定できるのは文字列のみ  

---

なんでこんな事してるかというと Lambda 経由で SES メールを送る時にメールの定義を外出ししたいなと思い  
何か方法が無いかなと調べる中で SES Template というのがあったので使ってみた感じ  

昔からある機能だが 管理コンソール上で確認や操作が出来ない、  
Template 化しても変数の扱いなどかゆい所に届かない感じだったり  
イマイチネット上でナレッジも無いし API V2 では CreateTemplate 等がなくなっている  

ちょろっと使って最悪利用を辞める事になても問題無いならいいけど、  
ガッツリこの仕組に依存するのは止めたほうが良さそう  

Lambda で SESから カスタマイズされたメールを送りたいときには便利なんだけどね  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
