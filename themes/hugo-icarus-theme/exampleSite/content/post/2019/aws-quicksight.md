+++
title = "AWS QuickSight でなんちゃってBIしてみる"
tags = ["aws"]
categories = ["SE"]
date = "2019-10-31"
description = "高額なBIツールとデータ準備は面倒だけど、AWS QuickSight と Excel(CSV) を使ってなんちゃってBIできるか検証してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## QuickSight
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/quicksight/" data-iframely-url="//cdn.iframe.ly/MVxY593?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金プラン
QuickSight には **Enterprise Edition** と **Standard Edition** がある比較は下記のごとく  
<i class="fas fa-external-link-alt"></i> [Amazon QuickSight の料金表](https://aws.amazon.com/jp/quicksight/pricing/?nc=sn&loc=4)  
<img src="/images/2019/aws-quicksight/qs-create01.png" />  

簡単な違いとしては、  

* StanderdEditionだと作成者以外そのデータは見れない  
* SPICEデータの利用料金  
* VPC接続サポートの有無  
* ActiveDirecotryサポートの有無  
* 通知、レポートの有無  

その他もろもろカスタマイズって感じかな  

### StanderdEdition
検証と個人的な興味なので**StanderdEdition**でやる  
どちらの料金プランでも、**年間サブスクリプション**での考えがあることに注意  

年間サブスクリプションでのユーザーあたりの月額料金が 9 USD  
ユーザーあたりの月額料金が 12 USD  
ユーザーあたり 10 GB の SPICE 容量/追加容量 1 GB あたり 0.25 USD  

年使うとお得  
とわいえ検証で使うには割と高い  

## QuickSightの開始  
<img src="/images/2019/aws-quicksight/qs-create02.png" />  
<img src="/images/2019/aws-quicksight/qs-create03.png" />  

もちろん StanderdEdition を選ぶ  

<img src="/images/2019/aws-quicksight/qs-create04.png" />  
<img src="/images/2019/aws-quicksight/qs-create05.png" />  

今回は CSV（Excel）データの分析を想定しているのでホイホイとすすめる  

<img src="/images/2019/aws-quicksight/qs-create06.png" />  

IAM = 作成ユーザー名で紐付きそうなので、QuickSight を使うユーザーは専用の IAM を払い出したほうが良さそう  

### データの登録
開発アカウントの登録が完了しログインすると最初はこんな感じ  

<img src="/images/2019/aws-quicksight/qs-create07.png" />  

右上には現在の IAM 情報が記載されている。作成したユーザーは何だったんだ？  

左上の「新しい分析」から分析したいデータを登録していく  

<img src="/images/2019/aws-quicksight/qs-create08.png" />  

まだ何もしていないので右上の「SPICE容量」は「0バイト使用済み」になっている  
どれくらいの期間利用しているのか、どれくらいのデータ容量利用しているのかは料金に響くため、  
右上のアカウント情報などからこまめに確認しておく  

サンプルは無視し、新しいデータセットを作成する  
意外と多くのデータソース参照元をサポートしている  

<img src="/images/2019/aws-quicksight/qs-create09.png" />  
<img src="/images/2019/aws-quicksight/qs-create10.png" />  

CSVでアップしたいので「ファイルのアップロード」を選択する  
サポートしているデータ形式は `.csv`、`.tsv`、`.clf`、`.elf`、`.xlsx`、`.json`  

ちなみに日本語文字ぶっこんだら文字化けしてた  

<img src="/images/2019/aws-quicksight/qs-create11.png" />  

ので「設定の編集とデータの準備」でデータを修正、調整する  

<img src="/images/2019/aws-quicksight/qs-create12.png" />  

データ型もあってるかちゃんと確認する  

問題なければ保存し、可視化ツールで各値、グラフ描画ツールを使いデータを分析出来る  

<img src="/images/2019/aws-quicksight/qs-create13.png" />  

---

使ってみた感想はそもそも BI、データ・アナリティクスに対する知識がなさすぎるので  
どういった解析をしたいのか？そのためにはどのようなデータが必要か？  
BIで解析しやすくするためにはどのようなデータ構造が良いか？  

を全く考えずに手持ちのデータで始めてみたが、QuickSight 自体のとっつきやすはあり、  
ちゃんとデータを準備して、高額なBIツールはまだ早いけど、データ分析してみたいなという場合には良いと思った  

ちょっとちゃんとデータの準備や分析のビジョンができたらもう一回トライしてみたい  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4822258912/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/512CRueOBKL._SL160_.jpg" alt="最強のデータ分析組織 なぜ大阪ガスは成功したのか" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
