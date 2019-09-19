+++
title = "仕事で意外と使う Webサービス"
tags = ["Web"]
categories = ["diary"]
date = "2019-09-19"
description = "そんな使うわけじゃないけどあると便利で無いと意外と困る、仕事等で使うWebサービス"
menu = ""
banner = "banners/banner_school.jpg"
images = []
+++

<!--more-->

## 情報収集系  
### Gauge 脆弱性情報通知サービス  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://vns.vital-service.com/" data-iframely-url="//cdn.iframe.ly/WZ3vUqf?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

JVN等々から新着の脆弱性情報や更新された脆弱性情報をメールで通知してくれる  
JVNの深刻度に基づいて上位のものだけ通知とかも出来るので影響が高そうな脆弱性情報の情報キャッチに使える  

### IPA 情報処理推進機構
<i class="fas fa-external-link-alt"></i> [https://www.ipa.go.jp/security/index.html](https://www.ipa.go.jp/security/index.html)  

世の中的に本当に影響がありそうな脆弱性情報とかはここで確認  

### JVN
<i class="fas fa-external-link-alt"></i> [https://jvn.jp/](https://jvn.jp/)  

もうちょっとエンジニア向けの脆弱性情報はここで確認  

### JPCERT コーディネーションセンター  
<i class="fas fa-external-link-alt"></i> [https://www.jpcert.or.jp/](https://www.jpcert.or.jp/)  

広く浅く脆弱性情報の確認に  

### AWS 週間AWS
<i class="fas fa-external-link-alt"></i> [https://aws.amazon.com/jp/blogs/news/tag/週刊aws/](https://aws.amazon.com/jp/blogs/news/tag/週刊aws/)  

AWSサービスの情報収集に  


## 業務で使う系
### Qualys SSL Labs SSL Server Test
<i class="fas fa-external-link-alt"></i> [https://www.ssllabs.com/ssltest/](https://www.ssllabs.com/ssltest/)  

有名なWebサイトのSSL強度を診断してくれるサイト  
Webサイト立てたらとりあえずここで診断してブラウザの対応状況とか脆弱性の穴を確認するよね  

### Mozilla SSL Configuration Generator
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://ssl-config.mozilla.org/" data-iframely-url="//cdn.iframe.ly/wcrGgp1"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

ApacheなどWebサーバサイドでSSL暗号化するときの最適な設定内容をアドバイスしてくれる  
「Qualys SSL Labs SSL Server Test」と合わせて最強のSSL Webサイトを目指そう  

### ShellCheck shell script analysis tool
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.shellcheck.net/" data-iframely-url="//cdn.iframe.ly/iTQUbZ"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

Linuxのシェルスクリプトを構文チェックしてくれるサイト  
非推奨の記述やシェルが上手く動かないときの問題箇所判定とかに利用してる  

### TECH-UNLIMiTED CSRの内容確認ツール
<i class="fas fa-external-link-alt"></i> [http://tech-unlimited.com/csrcheck.html](http://tech-unlimited.com/csrcheck.html)  

SSL証明書を作成するときにはいつも不安  
作成したCSRの内容は本当に間違いないか？を確認しよう  

### Google Developers PageSpeed Insights
<i class="fas fa-external-link-alt"></i> [https://developers.google.com/speed/pagespeed/insights/?hl=ja](https://developers.google.com/speed/pagespeed/insights/?hl=ja)  

Googleが提供している多数のサービスの一つ、Webサイトの表示速度を診断してくれる  
何が遅い原因なのかもアドバイスしてくれる  

### imgix Page Weight Tool
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://pageweight.imgix.com/" data-iframely-url="//cdn.iframe.ly/I3FgfC4?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

Webサイトの重さを診断してくれる  
「Google Developers PageSpeed Insights」と似ていて主に画像に特化しているけど軽くするためのアドバイスをしてくれる  

### Newsletters spam test by mail-tester.com
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="http://www.mail-tester.com" data-iframely-url="//cdn.iframe.ly/NcHngN4"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

サービスで飛ばそうとしているそのメール、大丈夫？
SPAMに間違われないようになっているかメールの信頼度を判定してくれる  

### Sender Policy Framework (SPF) Record Lookup - SPF Check
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="http://mxtoolbox.com/Public/Content/Toolhandler.aspx?command=spf" data-iframely-url="//cdn.iframe.ly/tPLhkhQ?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

SFPレコードの登録状況を診断してくれる  

### NTP Pool Project
<i class="fas fa-external-link-alt"></i> [https://www.pool.ntp.org/zone/asia](https://www.pool.ntp.org/zone/asia)  

世界中の NTP Pool を参照できる  

### Mozilla Observatory
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://observatory.mozilla.org/" data-iframely-url="//cdn.iframe.ly/Gy4dVfW"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

Webサイトの通信、ヘッダー等からサイトの脆弱性、セキュリティ対応状態を診断してくれる  


## 事務作業で使う系
### Cloudcraft Draw AWS diagrams  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://cloudcraft.co/" data-iframely-url="//cdn.iframe.ly/V9J4E01"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

AWSの構成図をちょっとおしゃれに簡易的に表現できるツール  
書いた図は画像ファイルとして保存できる  

### Flowchart Maker & Online Diagram Software
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.draw.io/" data-iframely-url="//cdn.iframe.ly/I8jJjg"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

「Cloudcraft Draw AWS diagrams」より構成図やフロー図などもっと標準的に色々できるサービス  
書いた図は独自形式のファイルや画像として保存できる  

### Markdown Tables Generator
<i class="fas fa-external-link-alt"></i> [https://www.tablesgenerator.com/markdown_tables](https://www.tablesgenerator.com/markdown_tables)  

Markdownで Table 書くの面倒だよね  

### Generate Responsive Embed Codes via Iframely
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://iframely.com/embed" data-iframely-url="//cdn.iframe.ly/vxL9oT"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

このサイトでも使ってる  
外部サイトリンクをおしゃれな iframe 形式のコードにしてくれるサイト  

### 画像圧縮系
<i class="fas fa-external-link-alt"></i> [squoosh](https://squoosh.app/)  
<i class="fas fa-external-link-alt"></i> [Optimizilla オンラインイメージ最適化ツール](https://imagecompressor.com/ja/)  

同類のサービスは他にもたくさんあるのでお好きなのをどうぞ  

### Remove Background from Image
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.remove.bg/" data-iframely-url="//cdn.iframe.ly/EwSDC7A?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

画像の背景をトリミングしてくれる  

### バイト換算
<i class="fas fa-external-link-alt"></i> [http://www.mie.to/123/b.html](http://www.mie.to/123/b.html)  

似たようなサイトは幾つもあるけど、シンプル、ゆえに便利  
でもいつかこのサイト無くなってそう  

### CMAN サブネットマスク計算（IPv4)
<i class="fas fa-external-link-alt"></i> [https://note.cman.jp/network/subnetmask.cgi](https://note.cman.jp/network/subnetmask.cgi)  

似たようなサイトは幾つもあるけど、最初に出会ったサイトを長く使い続けることってあるよね  
サブネットマスク一覧表は地味に便利  


{{< rawhtml >}}
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797393165/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/513hw-04L8L._SL160_.jpg" alt="体系的に学ぶ 安全なWebアプリケーションの作り方 第2版 脆弱性が生まれる原理と対策の実践" style="border: none;" /></a>
{{< /rawhtml >}}
