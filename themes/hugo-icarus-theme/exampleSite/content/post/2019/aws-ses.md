+++
title = "Postfix と連携して AWS SES からメール送信"
tags = ["aws"]
categories = ["SE"]
date = "2019-09-09"
description = "AWS SES でのメール送信再入門"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->
{{< googlead >}}

## SES
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/ses/" data-iframely-url="//cdn.iframe.ly/h1S2bPS?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

利用出来るリージョンが指定されているため注意  
送信に限ってい言えばサーバから送る場合は 毎月送信する E メールのうち最初の 62,000 通につき 0 USD、それ以降 1,000 通ごとに 0.1USD  

## 
<i class="fas fa-external-link-alt"></i> [Amazon SES を介して E メールを送信する](https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/sending-email.html)  

### 認証情報の取得
SES連携時に利用する認証情報の取得  

<img src="/images/2019/aws-ses/ses_setup01.png" />  
<img src="/images/2019/aws-ses/ses_setup02.png" />  
<img src="/images/2019/aws-ses/ses_setup03.png" />  

### Eメールアドレスの検証
送信元として利用するアドレス、またはドメインが管理しているものであり正規のものであることを承認します  
メール認証はそもそもメールを送受信するサーバがほしいのに受信先が無いのでドメイン認証を行う  

<img src="/images/2019/aws-ses/ses_setup04.png" />  
<img src="/images/2019/aws-ses/ses_setup05.png" />  
<img src="/images/2019/aws-ses/ses_setup06.png" />  

Route53 を利用している場合は「Create Route53」を指定するだけで登録してくれる  
ちなみに MX レコードにチェックを入れていると下記のようなメッセージが出てくる  

> WARNING: This option will replace all existing MX records for your domain. Do NOT select this option unless you are specifically setting up your domain to receive email through Amazon SES, as described in Receiving Email with Amazon SES.  

受信は想定していないので登録しない  

少しすると検証が「verified」になる  
<img src="/images/2019/aws-ses/ses_setup07.png" />  

### SES sandboxモードの解除  
SES はデフォルトでは Eメール検証に認証したメールか特定のメールにしか送れないようになっているため外部にメールを送りたい場合は解除申請が必要  
<i class="fas fa-external-link-alt"></i> [AWS サポート 制限解除](https://console.aws.amazon.com/support/cases#/create?issueType=service-limit-increase&limitType=service-code-ses)

<img src="/images/2019/aws-ses/ses_setup08.png" />  

これを知らずにやるとメール送信しても下記のようなエラーで拒否される（された）  

```
host email-smtp.us-east-1.amazonaws.com[18.214.81.247] said: 554 Message rejected: Email address is not verified. The following identities failed the check in region US-EAST-1
```

### Postfixと連携
<i class="fas fa-external-link-alt"></i> [Amazon SES と Postfix の連携](https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/postfix.html)  

必要パッケージのインストール  

```
# yum install cyrus-sasl-plain
```

Postfixリレー設定  

```
# vim /etc/postfic/main.cf

# SES
relayhost = [email-smtp.us-east-1.amazonaws.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_security_options = noanonymous
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_use_tls = yes
smtp_tls_security_level = encrypt
smtp_tls_note_starttls_offer = yes
```

`master.cf` の `smtp_fallback_relay` をコメントアウトする  
ちなみになかったのでスルー  

```
# grep smtp_fallback_relay /etc/postfix/master.cf
```

作成した認証情報の登録

```
# vim /etc/postfix/sasl_passwd
[email-smtp.us-west-2.amazonaws.com]:587 SMTPUSERNAME:SMTPPASSWORD
```

ハッシュ化  

```
# postmap /etc/postfix/sasl_passwd
# chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
```

Amazon SES サーバー証明書を検証するためにPostfix が CA 証明書の場所を認識できるようにします  

```
# vim /etc/postfic/main.cf

smtp_tls_CAfile = /etc/ssl/certs/ca-bundle.crt
```

設定の反映  

```
# postfix check
# systemctl restart postfix
```

送信確認  

```
# mail -s TEST -r info@hogehoge.com user@mugemuge.com
```

受信したメールを見てみると下記の感じで SPF、DKIM も PASS になってた  

<img src="/images/2019/aws-ses/ses_setup09.png" />  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
