+++
title = "Amazon S3 Webホスティングで利用する場合の注意点"
tags = ["aws"]
categories = ["SE"]
date = "2019-12-02"
description = "Amazon S3 を Webホスティングして CloudFront と合わせて利用する場合の注意点まとめ"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## S3 Webホスティング  
S3 での Webホスティングについては下記を確認  
[Amazon S3 での静的ウェブサイトのホスティング](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/WebsiteHosting.html)  

## 独自ドメインでSSL化したい場合は CloudFront
[Amazon S3 でホストされている静的ウェブサイトを提供するために CloudFront をどのように使用したらよいですか ?](https://aws.amazon.com/jp/premiumsupport/knowledge-center/cloudfront-serve-static-website/)  

構成についてはマニュアル参照、以下に同構成を取る場合の注意点を書いておく  

### 307リダイレクト
[Amazon S3 から HTTP 307 Temporary Redirect レスポンスが返るのはなぜですか?](https://aws.amazon.com/jp/premiumsupport/knowledge-center/s3-http-307-response/)  

S3 + CloudFront 形式で新規作成した場合、  
S3 のエッジと CloudFront のエッジ環境が整いきるまでは 307 リダイレクトで S3 の URL にリダイレクトされてしまう仕様  
最大24時間かかることもある  

### 多階層時のディレクトリインデックス
[CloudFront ディストリビューションのオリジンとして S3 ウェブサイトのエンドポイントを使用しています。403 Access Denied (アクセス拒否) エラーが発生するのはなぜですか?](https://aws.amazon.com/jp/premiumsupport/knowledge-center/s3-website-cloudfront-error-403/)  

[CloudFront がサブディレクトリからデフォルトのルートオブジェクトを返さないのはなぜですか?](https://aws.amazon.com/jp/premiumsupport/knowledge-center/cloudfront-default-root-object-subdirectory/)  

CloudFrontでS3をエンドポイントに設定する場合  
S3 形式のエンドポイントにするか、~~REST API~~ Webホスティング 形式で S3 を指定するかで表示の挙動が変わる  

参照記事にあるように  

<i class="fas fa-chevron-circle-right"></i> S3 エンドポイント形式（REST API）の場合  
リクエスト：https://hogehoge.com/　→ CloudFront+S3：https://hogehoge.com/index.html  
リクエスト：https://hogehoge.com/hogehoge/　→ CloudFront+S3：403  

CloudFront のオリジン指定時で S3 バケットを指定した場合の話  
S3のディレクトリインデックス指定は ~~REST API~~ Webホスティング 時の挙動設定なので  
CloudFront でインデックスを指定しても TOP 時しかうまく動かない  

<i class="fas fa-chevron-circle-right"></i> S3 Webホスティング 形式  
リクエスト：https://hogehoge.com/　→ CloudFront+S3：https://hogehoge.com/index.html  
リクエスト：https://hogehoge.com/hogehoge/　→ CloudFront+S3：https://hogehoge.com/hogehoge/index.html  

~~REST API~~ Webホスティング形式のオリジン（S3のWebホスティングURL）にすれば S3 のディレクトリインデックス指定が有効に動く  
しかし ~~REST API~~ Webホスティング形式の場合記事にあるように S3 を完全公開する必要があったりするため把握しておく  

Webホスティングにした場合、オリジンに対する OAI が使えない  
URLをバラさない限り直アクセスは無いとは思うがその点は注意する  

### SSIの利用  
使えません（らしい）  
明言しているソースは見つからなかったのだが、動かないことは確認できる  
下記のような記述は動かない  

```
<!--#include virtual="i/header.htm" -->
<!--#echo var="i/header.htm" -->
<!--#include file="i/header.htm" -->
```

まぁそもそも Server Side Include っていってるんだからサーバ無いしね  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
