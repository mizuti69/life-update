+++
title = "AWS Global Accelerator は 災害対策に使えるか"
tags = ["aws"]
categories = ["SE"]
date = "2019-08-07T03:00:00+09:00"
description = "AWS でリージョンを跨いだ災害対策環境を構築するのに Global Accelerator は使えるのか検証してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS Global Accellerator
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/global-accelerator/" data-iframely-url="//cdn.iframe.ly/OXvyT1H?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金  
<i class="fas fa-external-link-alt"></i> [AWS Global Accelerator の料金](https://aws.amazon.com/jp/global-accelerator/pricing/)  
アクセラレーターが削除されるまで、あなたのアカウントでアクセラレーターが稼働している 1 時間 (1 時間未満は繰上げ) 毎に 0.025 USD 課金  
AWS ネットワーク経由で転送されるデータのギガバイトあたりの料金  

この辺も ELB とかと同じくベース + 転送量  

## 検証

### 環境準備
現在対応している転送先は ALB か NLB か EIP になっているので  
東京リージョン、シンガポールリージョンにそれぞれ ALB(80) + EC2(80) の環境を用意する  
global Accelerator の転送先に指定できるリージョンは下記  

* us-east-1
* us-east-2
* us-west-1
* us-west-2
* eu-west-1
* eu-central-1
* ap-northeast-1
* ap-southeast-1
* ca-central-1
* eu-west-2
* eu-west-3
* ap-southeast-2
* ap-northeast-2
* ap-south-1

<i class="fas fa-chevron-circle-right"></i> ap-northeast-1 環境  
<img src="/images/2019/aws-globalaccelerator/accelerator_setup01.png" />  

<i class="fas fa-chevron-circle-right"></i> ap-southeast-1 環境  
<img src="/images/2019/aws-globalaccelerator/accelerator_setup02.png" />  

### Global Accelerator の作成
<img src="/images/2019/aws-globalaccelerator/accelerator_setup03.png" />  
<img src="/images/2019/aws-globalaccelerator/accelerator_setup04.png" />  

検証なのでとりあえず 80 番ポートを  
CloudFront みたいに HTTPS 選んでも オレゴンとかの ACM から証明書引っ張ってこれるのかな?  

<img src="/images/2019/aws-globalaccelerator/accelerator_setup05.png" />  

現時点ではリンクある help を押してもドキュメントに飛ばないので手探り  
災害時を想定するため 正常系（東京リージョン）に 100 、 DR用（シンガポール環境）を 0 にしてみる  

<img src="/images/2019/aws-globalaccelerator/accelerator_setup06.png" />  

エンドポイントの指定  
エンドポイント単位でトラフィックの割合をさらに細分化できる  

<img src="/images/2019/aws-globalaccelerator/accelerator_setup07.png" />  

作成  

<img src="/images/2019/aws-globalaccelerator/accelerator_setup08.png" />  
<img src="/images/2019/aws-globalaccelerator/accelerator_setup09.png" />  

Global Accelerator 自体にアクセス制御的な機能は無いようだ、この辺も CloudFront と似てる  
ただ ALB 自体はまだ セキュリティグループで特定 IP からのみアクセス許可としているんだけど ステータスは ` All healthy` になっている  
通信できるか、ではなくエンドポイントのステータスを見てるんかな、きっと  

この状態で払い出された GIP にアクセスしてもタイムアウトした  
ALB 上は許可している IP 拠点からでも拒否されたので、 Accelerator を利用する場合のアクセス制御は AWS 側では無理そう  
一時的に ALB のセキュリティグループを全許可してアクセスしたら表示された  

サーバ側のアクセスログで見ると下記の感じ  

```
10.20.0.94 "99.82.164.21" - - [06/Aug/2019:17:23:46 +0000] "GET / HTTP/1.1" 200 13 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/601.7.7 (KHTML, like Gecko) Version/9.1.2 Safari/601.7.7"
```

クライアント IP 情報は `X-Forwarded-For` ヘッダーにも載ってこない  
上記の `"99.82.164.21"` で囲まれた IP がそのヘッダーだが、これはおそらく Accelerator の IP で、アクセスする度に変わっていた  

まぁ、Accelerator 作成中にも「Global Accelerator does not preserv client IP address when routing traffic to endpoints」っていってたからそうなんだけど  
アクセス解析や IP制限 を行いたい場合とかは注意が必要  

### バランシング検証
100 - 0 の割合にして、100 側のサービスを落としたら、普通に災害用環境に振られた  
切り替わり時間は測って無いけど体感数分くらい。10分はかかってないと思う  

<img src="/images/2019/aws-globalaccelerator/accelerator_setup10.png" />  
<img src="/images/2019/aws-globalaccelerator/accelerator_setup11.png" />  

じゃぁ 100 - 100 だったら？

<img src="/images/2019/aws-globalaccelerator/accelerator_setup12.png" />  
<img src="/images/2019/aws-globalaccelerator/accelerator_setup11.png" />  

シンガポール側が表示された、あたりまえ  

100 - 100 で両方サービスを起動してみる  

<img src="/images/2019/aws-globalaccelerator/accelerator_setup13.png" />  

ALB 側はセッション維持設定を行っていないが、 F5 連打するだけだと同一サーバに振られるだけだった  
5分～10分間隔くらいでアクセスするとたまに違う方にアクセス行くくらい（keep aliveかな？）  
もうちょっと半々くらいに両リージョンに振られるのかなと思ったけど  
ヘッダーも特に変わったところはなかったので、まぁなんか基板側で効率化してるんだろう  

<img src="/images/2019/aws-globalaccelerator/accelerator_setup14.png" />  

かんたんに検証してみたけど、災害対策としては R53 のヘルスチェック切り替えに次いでの方法が出てきてすぐにでも使えそうなのはいいこと  
ただアクセス制御ができないこと、アクセス元 IP 情報がわからなくなることに問題がある場合はまだやめとこう  
マニュアルが整備されてきたらその中に回避策があるかもしれない  
普通に使ってワールドワイドに展開するサービスのトラフィック最適化として使ってもお手軽  

なにより今まで当たり前のように ALB や CLB だから 外部DNSの場合は CNAME 使わないとね～という成約が無くなるのは嬉しい  
Global Accelerator は CloudFront とは併用できるのかな？  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
