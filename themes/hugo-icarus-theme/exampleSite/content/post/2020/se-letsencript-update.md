+++
title = "Let's Encrypt certbot を ACMEv2 にアップデートする"
tags = ["linux"]
categories = ["SE"]
date = "2020-02-03"
description = "Let's Encrypt 証明書発行プロトコル ACMEv2 廃止に伴う ACMEv2 アップデート"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## End of Life Plan for ACMEv1 
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://community.letsencrypt.org/t/end-of-life-plan-for-acmev1/88430" data-iframely-url="//cdn.iframe.ly/rGT4O1L"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

下記のようなメールが来て  

> According to our records, the software client you're using to get Let's  
> Encrypt TLS/SSL certificates issued or renewed at least one HTTPS certificate  
> in the past two weeks using the ACMEv1 protocol. Your client's IP address was:  
> 
> <IP Address>  
> 
> Beginning June 1, 2020, we will stop allowing new domains to validate using  
> the ACMEv1 protocol. You should upgrade to an ACMEv2 compatible client before  
> then, or certificate issuance will fail. For most people, simply upgrading to  
> the latest version of your existing client will suffice. You can view the  
> client list at: https://letsencrypt.org/docs/client-options/  
>  
> If you're unsure how your certificate is managed, get in touch with the  
> person who installed the certificate for you. If you don't know who to  
> contact, please view the help section in our community forum at  
> https://community.letsencrypt.org/c/help and use the search bar to check if  
> there's an existing solution for your question. If there isn't, please create  
> a new topic and fill out the help template.  
>  
> ACMEv1 API deprecation details can be found in our community forum:  
> https://community.letsencrypt.org/t/end-of-life-plan-for-acmev1  
> 
> As a reminder: In the future, Let's Encrypt will be performing multiple  
> domain validation requests for each domain name when you issue a certificate.  
> While you're working on migrating to ACMEv2, please check that your system
> configuration will not block validation requests made by new Let's Encrypt IP  
> addresses, or block multiple matching requests. Per our FAQ  
> (https://letsencrypt.org/docs/faq/), we don't publish a list of IP addresses  
> we use to validate, and this list may change at any time.  
> 
> To receive more frequent updates, subscribe to our API Announcements:  
> https://community.letsencrypt.org/t/about-the-api-announcements-category  
>  
> Thank you for joining us on our mission to create a more secure and privacy-  
> respecting Web!  

どうやら 使ってる certbot のSSL証明書発行プロトコルが ACMEv1 で発行依頼来ているため  
「2020年6月1日から使えなくなるからアップデートしてね」とのことなのでアップデートする  

### アップデート 
利用している certbot が **v0.22** 以上であればいいらしい  
使ってる環境は epel のリポジトリからインストールしているのでそのまま yum ってみる  

```
# rpm -qa |grep certbot
python2-certbot-0.18.1-1.el7.noarch

# yum update
================================================================================================================
 Package                              アーキテクチャー   バージョン                   リポジトリー         容量
================================================================================================================
更新します:
 certbot                              noarch             1.0.0-1.el7                  epel                 41 k
```

v1.0.0 で要件は超えてそう  
最新かはわからないけどとりあえず大丈夫かな？  
ACMEv2 では v1 のときとリクエストする API URL が違うそうだが、certbot を使ってる限りアップデートしておけば意識しなくてよさそう  
次の更新時がどうなるか（再取得か更新で済むか）くらいかな  

## ACMEv1 / ACMEv2 ってなに？  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://free-ssl.jp/docs/acme-v2-wildcards.html" data-iframely-url="//cdn.iframe.ly/lPtcXsD"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 動作の仕組み  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://letsencrypt.org/ja/how-it-works/" data-iframely-url="//cdn.iframe.ly/9sueXGD"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  


### ACMEv2 になって変わること  

* v1 APIの既存のACMEアカウントはv2 APIで機能します。v1 APIからの既存の承認はv2 APIでは使用できません。つまり、v2 APIで発行する前にすべてのドメインを再承認する必要があります  
* 1つのACMEアカウントは、一度に300件までの未決注文を処理できます  
* V2 APIは、ワイルドカード証明書の発行をサポートしています  
* ACME v1 では、第三者による予測が困難な認可 ID（Authorization's ID）が用いられていました  
  この方針は透明性を高める目的で大きく変更されており、ACME v2 による認可の際には予測可能な連番 ID 番号による管理が行われます  
  そのため、ACME v2 ではドメイン認証の試行に関する下記の情報が第三者によって取得される恐れがあります  

などなど  
メインで影響うけそうなところは上記くらい？  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4908686009/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/41y5jGy1GrL._SL160_.jpg" alt="プロフェッショナルSSL/TLS" style="border: none;" /></a>
</div>
{{< /rawhtml >}}