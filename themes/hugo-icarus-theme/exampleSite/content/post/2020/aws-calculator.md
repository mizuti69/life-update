+++
title = "AWS SIMPLE MONTHLY CALCULATOR vs AWS PRICING CALCULATOR"
tags = ["aws"]
categories = ["SE"]
date = "2020-02-18"
description = "AWS Pricing Calculator が新しい見積もりツールとして出てきてますが見積もり精度は変わったのか？"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWSの見積もり
AWSサービスの見積もりを作ろうとした場合  
多くの人は「AWS SIMPLE MONTHLY CALCULATOR」を使っていると思います  
<i class="fas fa-external-link-alt"></i> [AWS SIMPLE MONTHLY CALCULATOR](https://calculator.s3.amazonaws.com/index.html?lng=ja_JP)  

それに対してちょっと前に出てきたのが AWS Pricing Calculator  
<i class="fas fa-external-link-alt"></i> [AWS Pricing Calculator](https://calculator.aws/#/)  

UIが最近のAWS風に変わっていて、SIMPLE MONTHLY CALCULATOR よりも対応サービスが増えている  
今後のロードマップ的にはこちらに段々移行していくんだろうな  

## 見積もり比較  
よくある ELB + EC2 + RDS + S3 な環境を見積もって比較してみる  

見積もり前提  

| 項目 | 内容 |
| --- | --- |
| regions | ap-northeast-1 |
| EC2 | AmazonLinux, c5.large, IN/OUT Traffic 100GB/月, EIP 1 |
| EBS | 汎用SSD 100GB |
| RDS | MySQL, Multi-AZ, db.m5.large, 100GB |
| ELB(ALB) | 新規接続 10/秒, 平均接続時間 60秒, 平均リクエスト数 100 <br /> ターゲット当たりのトラフィック 100GB/月, 評価ルール平均 2 |
| S3 | 標準ストレージ　500GB, データ転送・受信 100GB/月 |

### SIMPLE MONTHLY CALCULATOR  

<img src="/images/2020/aws/aws-calculator/calculator-01.png" />  

### AWS Pricing Calculator

<img src="/images/2020/aws/aws-calculator/calculator-02.png" />  
<img src="/images/2020/aws/aws-calculator/calculator-03.png" />  
<img src="/images/2020/aws/aws-calculator/calculator-04.png" />  

### 比較
<i class="fas fa-chevron-circle-right"></i> **EC2**  
「SIMPLE MONTHLY CALCULATOR」と同等の項目を入力するには「Pricing Calculator」で高度な見積もりを選択する必要がある  
「SIMPLE MONTHLY CALCULATOR」で EIP を入力すると金額が発生した場合の料金が記載されるが、「Pricing Calculator」では非課金扱い  

同じ項目と数値を入れたつもりだったけど「Pricing Calculator」の方が高くなった  

<i class="fas fa-chevron-circle-right"></i> **RDS**  
「SIMPLE MONTHLY CALCULATOR」では転送量やバックアップの項目があるが、「Pricing Calculator」には無い  
同様に転送量の項目も無いため、「Pricing Calculator」では VPC サービスを追加して VPC内トラフィック 100GB/月 を追加してる  

こちらはほぼ同額となった、「Pricing Calculator」の方が 約1$ 安くなっておりインスタンスの基本料金に差異がある  
（同じ料金テーブル見てるわけじゃないのか・・・？）  

<i class="fas fa-chevron-circle-right"></i> **ELB**  
ELB（ALB）はほぼ同じ入力項目、金額となった  
「Pricing Calculator」の方が $0.05 安い  
（同じ料金テーブル見てるわけじゃないのか・・・？）  

<i class="fas fa-chevron-circle-right"></i> **S3**  
こちらはどちらも同じ金額になった  

単体で見てると「Pricing Calculator」の方が高くなりそうだったのに、総額で見ると「SIMPLE MONTHLY CALCULATOR」の方が高くなっている  
なぜだ  

> SIMPLE MONTHLY CALCULATOR： ＄511.54  
Pricing Calculator： ＄504.30  

差額を整理  

> Pricing Calculatorを基準とした場合
> 差額は　＄7.24/月  
> 
> * EC2：-＄5.04  
> * RDS：+＄0.94 ※Pricing CalculatorはVPCの転送量 ＄1 を加算   
> * ELB：+＄0.05  
> * S3：＄0  
> * AWS データ転送送信：+＄11.29  

詳細にサービス単位の差額は「SIMPLE MONTHLY CALCULATOR」が +7.24となり  
特に入力した覚えもない、「Pricing Calculator」にもない **AWS データ転送送信** という項目が増えている  

これはどこから来たんだ？各種サービスのトラフィック量から自動的に算出したのか？  
同様の値段にしようとした場合、各種サービスで指定した VPCで発生するであろう 受信データ、送信データを足すと近い金額になる  

<img src="/images/2020/aws/aws-calculator/calculator-05.png" />  
※RDSの転送量を擬似的に入力しているため VPC内転送量 100GB/月 = ＄1 増えてます  

---

以上のことから「Pricing Calculator」に移行する場合は注意が必要そう  

<i class="fas fa-chevron-circle-right"></i> メリット  

* 見積もり可能なサービスが多い  
* 項目の入力値や何が入力必要な項目なのかが選択によって動的に変わるのでわかりやすい  
* 各入力に対する金額の計算式が見れるので理解しやすい  

<i class="fas fa-chevron-circle-right"></i> デメリット  

* EC2、RDS等のスナップショット、バックアップストレージ試算がサービスの見積もり以外で考慮が必要  
* VPCのトラフィックについては自分で入力する必要がある  

上記のことからマネージドサービス系で、実はログ出力で S3 使ってたり系の料金が本当に精緻で出るのか少し不安になった  
特に複雑になりがちで事前に想定が難しい VPC の転送量が自動的に算出されないのは辛い  

今まで通り、基本的にはサービスページの見積もり詳細をみて何に金額が発生しそうか理解し  
見積もりツールを利用する方針にしておいた方が安全そう  

AWS Backup のサービスもなかったから、「SIMPLE MONTHLY CALCULATOR」で項目になるバックアップの項目を代入しようとすると EBS とか S3 になるのかな？  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
