+++
title = "CPDos 攻撃について"
tags = ["セキュリティ"]
categories = ["SE"]
date = "2019-10-24"
description = "Cache Poisoned Denial of Service(キャッシュ汚染型DoS攻撃、CPDoS) 攻撃について確認し、出来ることを考える"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## ニュース情報
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://gigazine.net/news/20191024-cache-poisoned-denial-service-cpdos/" data-iframely-url="//cdn.iframe.ly/IbEfx1O?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

本家  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://cpdos.org/" data-iframely-url="//cdn.iframe.ly/8e3BQqE?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

## 概要
かいつまんで読むと Webキャッシュを利用して本来のページではなくエラーページを意図的にキャッシュさせ  
サイトを運用できない状況に追い込む攻撃  

ではどのように Webキャッシュを意図的にエラー画面にさせるかというと簡単で、  
キャッシュ元のオリジンがエラーを返すような HTTPヘッダーを送りつけ、キャッシュが更新されるタイミングでエラー表示にさせるというもの  
どんなHTTPヘッダーが例としてあるかというと  

### HTTPヘッダーオーバーサイズ(HHO)

> HHOは、キャッシュサーバーと元サーバーの間の「制限バイト数の差」を突く攻撃です。例えば、攻撃者がキャッシュサーバーの制限より小さく元サーバーの制限より大きいサイズのリクエストを送信したとします。この場合、このリクエストはキャッシュサーバーの制限を満たしているので、キャッシュサーバーはリクエストを受理します。しかし、キャッシュサーバーが元サーバーに対してリクエストを送信すると、元サーバーのサイズ制限に引っかかってしまうため、キャッシュサーバーにはエラーページが返信されてしまいます。  

なるほど  
Webサイトが CDN を使っている場合 どこの CDN を使っているかは 応答ヘッダーを見ればわりとわかったりするし、  
AWSの CloudFront とかみたいにドキュメントが整備されているところは上記のような差を突く攻撃はわりと簡単にできそうだ  
世の中的にも大方の Webaサーバは Apache か Nginx が使われているだろうし  

<i class="fas fa-external-link-alt"></i> [AWS - Cloudfront カスタムオリジンの場合のリクエストとレスポンスの動作](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/RequestAndResponseBehaviorCustomOrigin.html)  

> リクエストの最大長と URL の最大長  
> パス、クエリ文字列 (ある場合)、ヘッダーを含め、リクエストの最大長は 20480 バイトです。  
> CloudFront はリクエストから URL を構築します。この URL の最大長は 8192 文字です。  
> リクエストまたは URL がこの制限を超えると、CloudFront は、リクエストヘッダーフィールドが長すぎることを示す HTTP ステータスコード 413 (Request Header Fields Too Large) をビューワーに返してから、ビューワーへの TCP 接続を終了します。  

<i class="fas fa-external-link-alt"></i>  [Apache - 基本パケット構造](https://httpd.apache.org/docs/trunk/ja/mod/mod_proxy_ajp.html#basppacketstruct)  

> パケットサイズ  
> 多くのコードでそうなっているのですが、パケットサイズの最大サイズは 8 * 1024 (8K) です。パケットの実際の長さはヘッダに エンコードされて入っています。  
> 
> パケットヘッダ  
> サーバからコンテナに送出されるパケットは 0x1234 で始まります。 コンテナからサーバに送られるパケットは AB (ASCII コード A と ASCII コード B) で始まります。この二バイトの後に、ペイロード長が (上記の形式で) 続きます。このため、ペイロード長の最大値は 2^16 にできるように思えますが、 実際にはコードでは最大値は 8K に設定されています。  

[Nginx - Module ngx_http_core_module](http://nginx.org/en/docs/http/ngx_http_core_module.html#large_client_header_buffers)  

> Sets the maximum number and size of buffers used for reading large client request header. A request line cannot exceed the size of one buffer, or the 414 (Request-URI Too Large) error is returned to the client. A request header field cannot exceed the size of one buffer as well, or the 400 (Bad Request) error is returned to the client. Buffers are allocated only on demand. By default, the buffer size is equal to 8K bytes. If after the end of request processing a connection is transitioned into the keep-alive state, these buffers are released.  

確かに記事のとおりのようだ  
CDNなどを利用していてキャッシュを最大限に活用したい場合多くはヘッダー情報でキャッシュの細分化などせず URL とクエリくらいだろう  
こんなの簡単にエラーを引き起こせてしまうやんけ  

### HTTPメソッドオーバーライド(HMO)

> HMOはHTTPメソッドを置換するようなヘッダを含んだリクエストです。途中で「POST」に置き換わるようにヘッダに書き込まれた「GET」リクエストを送信したとすると、キャッシュサーバーはこのリクエストは「GET」だと扱います。しかし、元サーバーでは「POST」として実行されるため、「POST」リクエストを処理することができるアプリケーションが埋め込まれていない場合には、404 Not Foundのエラーページを返してしまいます。  

例では **x-http-method-override** を使っている  
そんなヘッダーがあることすら知らなかったけど、主に API 界隈で利用されている GET や POST 以外を遮断されるようなネットワーク環境で  
API と やり取りする場合にどうしても DELETE などのリクエストを送りたい場合に使うヘッダーらしい  
RFPとかなんかあるのかと探してみたけどちょっと見つからなかった、拡張ヘッダーだからかな  
当然そんなヘッダーを送ったって受け取るサーバ側がサポートしてなければ意味は無いのだが、キャッシュサーバが間にあると話が変わってくるという話  

対応していない場合、そのヘッダーを解釈してリクエストメソッドを変更するのは Webサーバになりそうですが  
Aapche や Nginx がどうこうというドキュメントは見当たらなかった  

## 対策
ニュース記事にある通り、基本的に影響がヤバそうなのは **CloudFront** を使っている環境  
他は Webサーバとして **IIS** 、**ASP.NET** 、 **Play 1** くらい  

ただ攻撃方法がシンプルなだけあって防ぐ方法がぱっとは無いのが現状  
記事でも「エラーページをキャッシュしないように」くらいしか書いてない  

ほぼ AWS の CloudFront が問題なのでそれについて話すが、  
HMO については　CloudFront ＋ WAF 構成で利用しているのであれば指定ヘッダーの拒否等で回避できる  
HHO も WAF でヘッダーサイズ拒否とかできそうだがそのヘッダーは使いたい！という環境はもうだめだ  

<i class="fas fa-external-link-alt"></i> [AWS WAF - 文字列一致条件の使用](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/web-acl-string-conditions.html)  

ただ記事にもある通り、ユーザー側での対策は緩和策にしかならず CDN の仕様に左右される部分が大きいため  
提供サービス側が対応してくれることを期待したい  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/B017CUAM7G/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/517iSgObvGL._SL160_.jpg" alt="サイバーセキュリティ2020　脅威の近未来予測 (NextPublishing)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
