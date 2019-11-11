+++
title = "TLS1.3 HTTP/3 ちょっと調査"
tags = ["linux"]
categories = ["SE"]
date = "2019-11-11"
description = ""
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!-- more -->

## Redhat 8.1 リリース
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/8.1_release_notes/index" data-iframely-url="//cdn.iframe.ly/ipOb5TG"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

8.1 がリリースされ、ここと偶数マイナーバージョンから LTS が開始する  
やっと導入検討が具体的に出来るようになった  

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://access.redhat.com/support/policy/updates/errata" data-iframely-url="//cdn.iframe.ly/OERWFb1"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

大きなアップデートはなく、バグフィックスとかパッケージの更新がメインぽいけど  
dnf でインストールできる OpenSSL と Apache がそれぞれ TLS1.3 に対応したバージョン `OpenSSL 1.1.1 over`、`Apache 2.4.37 over` になっていた（ 8.0 からか？）  

## TLS1.3 HTTP/3 対応状況

### ブラウザの対応状況
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://forest.watch.impress.co.jp/docs/news/1138657.html" data-iframely-url="//cdn.iframe.ly/0pNk1MV?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

FireFox 、Google Chrome はだいぶ前に対応済  

### AWS
<i class="fas fa-external-link-alt"></i> [CloudFront サポートされているプロトコルと暗号](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/secure-connections-supported-viewer-protocols-ciphers.html)  
<i class="fas fa-external-link-alt"></i> [Application Load Balancer 用の HTTPS リスナーを作成する](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/create-https-listener.html)  

AWSは特別調べても日本語記事はなく、マニュアルみてもまだ対応はしていないようだ  

### GCP
<i class="fas fa-external-link-alt"></i> [Google Cloud での転送データの暗号化](https://cloud.google.com/security/encryption-in-transit/?hl=ja)  

さすが HTTP/3 の QUICK に関わってるだけあって GCP は対応し始めているよう  

### Azure
<i class="fas fa-external-link-alt"></i> [TLS 1.3 is now approved, this should be implemented with Web Apps](https://feedback.azure.com/forums/169385-web-apps/suggestions/35097916-tls-1-3-is-now-approved-this-should-be-implemente)  

サポートフォーラムに対する問い合わせくらいしか情報が引っかからないけど対応予定は未定のよう  
そもそも IE/Edge からまだ対応完了の予定が無い  
来年出る Chromium ベースの Edge から対応かな  

---

状況だけみるとクライアント環境はじゅんじゅんに対応していっているっぽいけど  
まだまだ普及、推奨化には程遠い整備状況な気がする  

とはいえ TLS1.0、1.1 は来年大手ブラウザはサポートを終了すると公表している  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.itmedia.co.jp/enterprise/articles/1810/16/news077.html" data-iframely-url="//cdn.iframe.ly/kmIeVNv?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

これで TLS1.2 に脆弱性でも出たら大変面倒なことになるので、早く対応していってほしいような  
インフラエンジニアとしては面倒なのでゆっくりしていってほしいような  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4908686009/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/41y5jGy1GrL._SL160_.jpg" alt="プロフェッショナルSSL/TLS" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
