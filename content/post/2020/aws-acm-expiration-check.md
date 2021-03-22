+++
title = "持ち込みインポート証明書でも ACM に登録して有効期限を監視する"
tags = ["aws"]
categories = ["SE"]
date = "2020-07-09"
description = "自動更新されない外から持ってきた証明書でも有効期限を監視して更新忘れ無いようにする"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS Config
AWS Config のルールを利用して ACM の有効期限前監視を行う  

Config では以下のルールで証明書の有効期限を定期的に監視して  
指定期限日以内になったら「非準拠」にする事ができる  
[AWS Config - acm-certificate-expiration-check](https://docs.aws.amazon.com/ja_jp/config/latest/developerguide/acm-certificate-expiration-check.html)  

AWS Config はアカウント単位管理のため、有効化してしまうと監視したいルール数に関わらず、  
利用サービス数に応じて一定金額発生するので注意する。それでも 1$ も行かないと思うけど  

### Config の設定
`acm-certificate-expiration-check` ルールで設定できるのは 有効期限が指定日時以下になったら「非準拠」とするか？のみ  

<img src="/images/2020/aws/config/rule-01.png" />  

監視対象の証明書を全てか、特定のリソースIDのみか、タグで制御し  
パラメータ `daysToExpiration` に指定日を設定する  

あくまで Config が定期監視したタイミングで指定日以下の有効期限を持つ証明書があるか？なので
14 と指定しても 13 日前、12日前になる可能性も考えられるため余裕を持った値にしておいたほうがいいかな  

## 通知設定
Config の標準機能である SNS 通知を設定してもいいんだけど  
複数環境の証明書を管理してて証明書単位で通知先を分けたい場合を考えて EventBridge で通知させる  

### EventBridge 設定  
EventBridge はイベントパターントリガーにして パターンを Config の `Config Rules Compliance Change` に設定する  

<img src="/images/2020/aws/config/rule-event-01.png" />  

ここでは通知先を SNS にしておく  
SNSの通知文言を 入力トランスフォーマー にして通知内容はシンプルにしておく  

<img src="/images/2020/aws/config/rule-event-02.png" />  

テンプレートは下記  

```
{"annotation":"$.detail.newEvaluationResult.annotation","resourceId":"$.detail.resourceId"}
```

```
"有効期限が近い証明書があります 証明書ARN：<resourceId> 有効期限：<annotation> "
```

これで Lambda とか色々考えなくても AWS のサービスだけで監視・通知できた  

---

この1ルールのためだけに Config を有効化するのは少しもったいないけど  
Config 自体は便利で CloudWatch だけでは出来ないことも組み合わせると色々できるし Config 自体も便利なのでこの為だけに使うということにはならないと思う  

ただ AWS のサービスのみで監視しているため融通がきかせにくいところもある  
今回の場合 EventBridge を `Config Rules Compliance Change` にしているため証明書を更新したタイミングでも  
コンプライアンスステータスが変わったら通知は飛んでしまう  

そこまで問題ないけど  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="https://www.amazon.co.jp/AWS%E8%AA%8D%E5%AE%9A%E3%82%BD%E3%83%AA%E3%83%A5%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%88-%E3%83%97%E3%83%AD%E3%83%95%E3%82%A7%E3%83%83%E3%82%B7%E3%83%A7%E3%83%8A%E3%83%AB-%E8%A9%A6%E9%A8%93%E7%89%B9%E6%80%A7%E3%81%8B%E3%82%89%E5%B0%8E%E3%81%8D%E5%87%BA%E3%81%97%E3%81%9F%E6%BC%94%E7%BF%92%E5%95%8F%E9%A1%8C%E3%81%A8%E8%A9%B3%E7%B4%B0%E8%A7%A3%E8%AA%AC-%E5%B9%B3%E5%B1%B1-%E6%AF%85/dp/4865942483/ref=as_li_ss_il?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=aws&qid=1594296050&sr=8-4&linkCode=li3&tag=sinokyoufu-22&linkId=b4975cf82152d3c6e34bf1c47773d412&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4865942483&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li3&o=9&a=4865942483" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}
