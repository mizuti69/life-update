+++
title = "自前メールサーバのPostfixにDKIM認証を追加する"
tags = ["postfix", "linux"]
categories = ["SE"]
date = "2019-08-28"
description = "自前でメールサーバを立てる場合、迷惑メールや受信拒否されないようにどこまで考えて対応できるか確認する"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.proofpoint.com/jp/products/email-protection" data-iframely-url="//cdn.iframe.ly/KCq9Tn7?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

きっかけは Apple などがメールプロテクションサービスとして利用している proofpoint にてメールサーバがブロックされるようになったこと  
proofpoint の大きな機能としては下記のようなものがある  

* **標的型攻撃対策**  
  保護された領域で実際に受信した添付ファイルを起動したり、メールに含まれるURLへアクセスし、不正プログラムの有無や挙動を判定するしくみ  
* **ＢＥＣ対策**  
  MARCレポート判定結果を自動的に解析・可視化するクラウド型ソリューション  
* **高機能メールセキュリティゲートウェイ**  
  大量に配信されるスパムやマルウェアはもちろん、アダルト、フィッシング、BECなどのきめ細かいフィルタリングで脅威を分類し、攻撃レベルによって適切なポリシーを適用し管理  

正規のメールであれば「標的型攻撃対策」でブロックされることは考えにくく（短縮URL等で拒否とかはありそうですが）  
基本的には BEC 判定の基準を満たせなくて拒否されているのかなと思う  

## セキュアなメールを目指す  
送信するだけなら誰でも簡単にメールは送れるけど、受信する側に信頼してもらうためには準備が必要  
<i class="fas fa-external-link-alt"></i> [有害情報対策ポータルサイト - 迷惑メール対策変 技術情報](https://salt.iajapan.org/wpmu/anti_spam/admin/tech/)  

### 名を名乗れ  
SMTPプロトコルでは 相手先に SMTP リクエストを送ったあと、 EHELO/HELO コマンドで互いのホスト名を宣言します  
その時に宣言するホスト名が正規に名前解決できるものか判断される  
なのでメールサーバを立てたときは利用するメールドメイン、メールサーバホスト名の A レコードを登録しておこう  

```
hogehoge.com A 192.168.1.1
```

しかし名前解決出来るかどうかだけであれば、  
送信側が所持しているメールドメインで無くても名前解決さえできれば問題ないことになってしまう  

### SPFレコードの設定
メールサーバの挨拶が終わり、さぁメール送る際  
例えば送信先に対してFromアドレス`from＠hogehoge.com`という差出人情報で送る場合  
私は（このメールサーバは）`＠hogehoge.com`のドメイン管理者からメール送信を許可されたサーバであることを宣言する必要がある  
そのための一つの仕組みとして DNS サーバの TXT レコードを利用したドメイン認証がある  

hogehoge.com の DNS サーバに 下記の様に登録することで、
指定のIPアドレスを持つメールサーバからのリクエストは ドメイン管理者から信頼されたメールサーバであると宣言出来ます  

```
"v=spf1 +ip4:192.168.1.1 -all"
```

上記の場合 指定 IP アドレスのメールサーバを認証し、それ以外は認証しない  

受信側は、SMTP接続してくる相手のIPアドレスと、SMTP通信でのMAIL FROM:コマンド（およびHELOコマンド）の引数として与えられるドメイン名（FQDN）を元に認証を実施する  
ドメイン名を元にSPFレコードを取得し、相手ホストのIPアドレスと照合して認証結果を得る  

### DKIM
SPFまででなんとなくメール送信をリクエストしてきたメールサーバは信頼できるような気がしてきました  
ではそのサーバが送ってくるメールは？  

DKIMでは送信側で電子メールに電子署名を付加し、受信側でその電子署名を照合するという方法で送信者のドメインの認証を行う  
メッセージのヘッダや本文を元に電子署名を作成するため、中継MTAなどで電子署名または電子署名の元になった電子メールのデータが変更されなければ、  
たとえメールが転送されたとしても転送先において認証が可能になる  

DKIMもドメイン認証方式のため DNS の TXT レコードに公開鍵を登録します  

```
dkim._domainkey.hogehoge.com TXT "v=DKIM1; k=rsa; p=****************************"
```

### 逆引き・MXレコード  
その他にも メールサーバの IP アドレスから逆引き検証を行うことで プロバイダから信頼できることを検証するメールサーバや、  
A レコードだけでなく MX レコードの登録状況を確認するようなメールサーバもあります  

大きくは A レコード、 SPF、DKIM を設定し、さらに信頼度をあげたい場合は 逆引き 設定を行うのがよさそう  

## PostfixでDKIM設定
SaaSやASPサービスでメール送信を行う場合は多くは上記の対応をしてくれている  
しかし自前で建てる場合でもやる必要があるので Postfix ではどうできるのか確認する  

### A レコード SPF レコード の登録
やっておこう  

### DKIM鍵の作成

DKIMサーバのインストール  

```
# yum install --enablerepo=epel opendkim
```

インストールするとユーザーが追加されてる  

```
# getent passwd
opendkim:x:990:987:OpenDKIM Milter:/var/run/opendkim:/sbin/nologin
```

鍵の作成  

```
# cd /etc/opendkim/keys/
# mkdir hogehoge.com
# cd hogehoge.com
# opendkim-genkey -D ./ -d hogehoge.com -s hoge
# ls -l
合計 8
-rw------- 1 root root 887  8月 28 21:47 hoge.private
-rw------- 1 root root 320  8月 28 21:47 hoge.txt
```

hoge.private : 秘密鍵  
hoge.txt : 公開鍵  

鍵ファイルの権限は OpenDKIMサーバから参照できるよう権限を変更しておく  

```
# chown opendkim: /etc/opendki
```

公開鍵の中身を確認し DNS に登録  

```
# cat hoge.txt
hoge._domainkey        IN      TXT     ( "v=DKIM1; k=rsa; "
          "p=******************" )  ; ----- DKIM key hoge for hogehoge.com
```

DNS登録内容  

```
hoge._domainkey.hogehoge.com TXT "v=DKIM1; k=rsa; p=********************"
```

### ADSP レコードの登録
Author Domain Signing Practice（ADSP）とは、DKIMの認証結果をどのように扱うべきかを示すポリシーを送信側で公開するもので  
DKIMではメールのDKIM-Signatureヘッダから認証対象のドメインを取り出すので、Fromヘッダに指定されている送信者アドレスのドメインと、署名したドメインが異なる場合がある  
ADSPはメール受信時にDKIM署名が有効ではない（認証結果のスコアが「dkim=pass」ではない）メールの取り扱いを、メールの送信者側が設定できる  

ADSPも、送信側のドメインを管理する権威DNSサーバーを使用してTXTレコードで公開する  

```
_adsp._domainkey.<ドメイン名> TXT "dkim=ADSP値"
```

| parameter | value |
| --- | --- |
| all | このドメインから送信されるメールは、すべてメール作成者署名が与えられる |
| unknown | このドメインから送信されるメールのいくつか、またはすべてに、メール作成者署名が得られる |
| discardable | このドメインから送信されるメールは、すべてメール作成者署名が与えられる。そして、もしメール作成者署名が得られない場合は、受信者はそのメールを破棄することが望まれる |

こちらもDNSに登録しておく  

### 登録状況の確認
登録内容を確認してみる  
<i class="fas fa-external-link-alt"></i> [SFP内容の確認](https://mxtoolbox.com/spf.aspx)  
<i class="fas fa-external-link-alt"></i> [DKIM内容の確認](https://dmarcian.com/dkim-inspector/)  

ここまで問題ないことを確認する  

### OpenDKIMの署名設定  

```
# vim /etc/opendkim.conf


##  Selects operating modes. Valid modes are s (sign) and v (verify). Default is v.
##  Must be changed to s (sign only) or sv (sign and verify) in order to sign outgoing
##  messages.
## 送信時に署名する  
Mode    s

##  Gives the location of a private key to be used for signing ALL messages. This
##  directive is ignored if KeyTable is enabled.
#KeyFile        /etc/opendkim/keys/default.private
KeyTable        refile:/etc/opendkim/KeyTable

##  Defines a table used to select one or more signatures to apply to a message based
##  on the address found in the From: header field. In simple terms, this tells
##  OpenDKIM how to use your keys. Requires KeyTable be enabled.
SigningTable    refile:/etc/opendkim/SigningTable

##  Identifies a set of "external" hosts that may send mail through the server as one
##  of the signing domains without credentials as such.
ExternalIgnoreList      refile:/etc/opendkim/TrustedHosts

##  Identifies a set "internal" hosts whose mail should be signed rather than verified.
InternalHosts   refile:/etc/opendkim/TrustedHosts
```

KeyTableの作成  

```
# vim /etc/opendkim/KeyTable

hoge._domainkey.hogehoge.com hogehoge.com:hoge:/etc/opendkim/keys/hogehoge.com/hoge.private
```

署名するメールドメインの指定  

```
# vim SigningTable

*@hogehoge.com hoge._domainkey.hogehoge.com
```

使用する Postfix サーバの指定  

```
# vim TrustedHosts

127.0.0.1
```

### OpenDKIMの起動  

```
# systemctl start opendkim
● opendkim.service - DomainKeys Identified Mail (DKIM) Milter
   Loaded: loaded (/usr/lib/systemd/system/opendkim.service; disabled; vendor preset: disabled)
   Active: active (running) since 水 2019-08-28 22:30:09 JST; 1s ago
     Docs: man:opendkim(8)
           man:opendkim.conf(5)
           man:opendkim-genkey(8)
           man:opendkim-genzone(8)
           man:opendkim-testadsp(8)
           man:opendkim-testkey
           http://www.opendkim.org/docs.html
  Process: 9224 ExecStart=/usr/sbin/opendkim $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 9225 (opendkim)
   CGroup: /system.slice/opendkim.service
           └─9225 /usr/sbin/opendkim -x /etc/opendkim.conf -P /var/run/opendkim/opendkim.pid

 8月 28 22:30:09 localhost opendkim[9225]: OpenDKIM Filter v2.11.0 starting (args: -x /etc/opendkim.conf -P /va....pid)
Hint: Some lines were ellipsized, use -l to show in full.
```

### Postfix の設定

```
# vim /etc/postfix/main.cf

smtpd_milters = inet:127.0.0.1:8891
non_smtpd_milters = inet:localhost:8891
milter_default_action = accept
```

反映  

```
# postfix check
# systemctl restart postfix
```

### 確認
メールテスト用サービスを使ってサーバからメールを送ってみる  
<i class="fas fa-external-link-alt"></i> [メールスパム判定](http://www.mail-tester.com/)  

下記のように MXレコード無いのは怒られてるが、SPF、DKIMにチェックが付いていることを確認できた  

<img src="/images/2019/se-postfix/postfix_dkim01.png" />  

まだ DMARC にはチェックついているが黄色のため改善点があること、その他にも減点項目を確認し修正すれば信頼度をあげられそうだ  

## DMARC を有効にする  
DMARCはSPF、DKIM両方をチェックし、準拠しない場合の挙動を設定するレコードになる  
詳細は下記 Googleのページがわかりやすい  
<i class="fas fa-external-link-alt"></i> [Google Gsuite - DMARCを有効にする](https://support.google.com/a/answer/2466563?hl=ja)  

例えば下記のようなレコードを登録しておくと  
とりあえずチェックはするけど拒否はせず、認証結果のレポートだけ担当者にメールするようになる  

```
_dmarc.hogehoge.com TXT "v=DMARC1; rua=mailto:webmaster@thogehoge.com; p=none; pct=90; sp=none"
```

無いよりはマシ  


{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4873110289/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/51Q8BAGD76L._SL160_.jpg" alt="電子メールプロトコル―基本・実装・運用" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
