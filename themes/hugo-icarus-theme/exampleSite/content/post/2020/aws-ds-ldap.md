+++
title = "AWS DirectoryService Managed MicrosoftAD との LDAP 連携"
tags = ["aws"]
categories = ["SE"]
date = "2020-06-01"
description = "AWS DS の Microsoft AD と サーバ側 LDAP を下位CA で連携する場合の手順"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/blogs/security/how-to-enable-ldaps-for-your-aws-microsoft-ad-directory/" data-iframely-url="//cdn.iframe.ly/p4LZJ9I?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

AWS の Directory Service で Managed Microsoft AD を利用した Ldap 連携の方法  
AWS の DirectoryService には Managed Microsoft AD と Samba を利用した Simple AD がある  
Ldap 連携やちょっとした用途なら Simple AD で良いんだけど Simple AD はロギング機能が無い  

Managed Microsoft AD ではその辺があるのだが管理や手順、ナレッジが極端に少ない  
基本的には上記リンクの手順通りやれば 下位CA を利用した Ldap 連携は出来るようになるんだけど日本語情報が少ないので書いておく  
ここに記載している手順も上記のサイトをそのまんまなぞる形になる  

## 事前準備  
Managed Microsoft AD を利用した 下位CAによる サーバ側 Ldap を有効にするには下記環境（一例）が必要になる  

* ルートCAを管理する Windows Server (EC2)  
* 下位CAを立て AWS DS と連携する Windows Server (EC2)  
* AWS DirectoryService Managed Microsoft AD  

下位CAサーバは AWS DS とドメイン連携している  
ここでは各種サーバ、サービスの用意までは割愛する  
特別な設定は無く、Windowsサーバは普通に立て、Managed Microsoft AD も最低限の構成で立てればいい  

また各 Windows Server 上での作業はドメイン管理者ユーザーでログインし操作しています  

### Windows Server への機能の追加
Windows Server を素の AMI で立てた場合は両方のサーバに下記機能を追加しておく  

* Active Directory ドメインサービス  
* Active Directory 認証サービス  

<img src="/images/2020/aws/ds/ad-ldap01.png" />  

### AD の管理者権限委任  
AD の 証明書管理権限を Windows Server (下位CA) サーバに委任させます  
「サーバーマネージャ」のメニューバーから「ツール」より「ユーザーとコンピュータ」を選択  

**AWS Delegated Groups** から **AWS Delegated Enterprise Certificate Authority Administrators** を選択し  
<img src="/images/2020/aws/ds/ad-ldap02.png" />  

CA管理ユーザに AWS証明書管理権限付を行います  
ここでは横着して Admin 管理者ユーザーに直接権限付与しています  

<img src="/images/2020/aws/ds/ad-ldap03.png" />  

OKを選択して設定を保存  
ここでは Admin 権限ユーザにて作業を行っているため手順では省いていますが  
CA管理用ユーザーを新規作成し手順どおりに作業を行っている場合は **AWS Delegated Server Administrators** グループにも追加して RDP 等々使えるようにしておきます  

### エンタープライズ CA 構築  
Windows CA サーバ側にて エンタープライズルート CA を作成します  

下記サイトが参考になりました  
<i class="fas fa-external-link-alt"></i> [Windows Server にルートCAの認証局の証明機関を構成する](https://www.ipentec.com/document/windows-server-setup-root-ca)

当環境は WindowsServer 2019 ですが基本的には同じ画面と手順になります  
こちらを参考に
**CA セットアップの種類** は エンタープライズを選択  
**CAの種類** は ルートCA を選択します  
それ以外はデフォルトのまま次へでいい感じ  

### ルート CA を 下位CA サーバにコピー  
CAサーバにて下記コマンドを PowerShell 当で実行し、証明書のコピーを取得  

```
> New-Item c:\rootcerts -type directory
> copy C:\Windows\system32\certsrv\certenroll\*.cr* c:\rootcerts
```

取得した CA ファイル一式を 下位CA用 Windows Server にコピーする  

下位CA サーバにて CA 取り込み用コマンドを実行  
CA ファイル、 CRL ファイルを ROOT証明書に登録する  

<img src="/images/2020/aws/ds/ad-ldap04.png" />  

```
> certutil –addstore –f root EC2AMAZ-CS07VBI.*************-EC2AMAZ-CS07VBI-CA.crt
> certutil –addstore –f root **********-EC2AMAZ-CS07VBI-CA.crl
> certutil –dspublish –f EC2AMAZ-CS07VBI.*************-EC2AMAZ-CS07VBI-CA.crt RootCA
```

### 下位CA の構築  
ルートCA サーバ側同様に 下位CA サーバ側でも CA を作成する  
ルートCA 側と違い **下位CA** をきちんと選択すること  

下位CA 作成後、下位CA サーバ側にて 上位のCA、ルートCA 側への証明書リクエスト要求ファイルを作成する  
PowerShell 等から下記を実行  

```
> New-Item c:\CARequest -type directory
> Copy c:\*.req C:\CARequest
```

証明書要求ファイルを `EC2AMAZ-U3F4B9A.***************-EC2AMAZ-U3F4B9A-CA.req` ルートCA サーバにコピーする  

### 下位CA 証明書要求の承認と発行  
ルートCA サーバ側でにて 証明書要求ファイルから証明書発行を承認する  
下記コマンドを実行し  

```
> certreq -submit EC2AMAZ-U3F4B9A.***************-EC2AMAZ-U3F4B9A-CA.req
```

ウィンドウが立ち上がってくるので OK を押す  
その後コマンドの結果には **権限の問題で失敗しました** みたいなメッセージが出てくるが問題無い  

「サーバーマネージャー」の「ツール」から「証明機関」を選択  
失敗した要求の中に先程実行したリクエストがあるのでこちらから発行する  
※根本的にこれで良いのかは不明  

<img src="/images/2020/aws/ds/ad-ldap05.png" />  

そうすると無事証明書が発行できていると思う  

<img src="/images/2020/aws/ds/ad-ldap06.png" />  

発行した証明書をエクスポートして 下位CA サーバに連携する  
要求ID は発行した証明書の要求ID のこと  

```
> certreq –retrieve <要求ID> <drive>:\subordinateCA.crt
```

### 証明書のインストール
下位CA サーバにて発行した証明書をインポートする  

```
> certutil –installcert c:\subordinateCA.crt
> start-service certsvc
```

## AD 連携
長ーい事前準備が終わったので AD 連携をを設定していく  

### 証明書テンプレートの作成  
下位CA 側にて証明書テンプレートを作成する  
「サーバーマネージャー」の「ツール」から「証明機関」を選択  

証明書テンプレートの項目を右クリックし **管理** を選択  
**Domain Controller** を右クリックし **テンプレートの複製** を選択  
[手順](https://aws.amazon.com/jp/blogs/security/how-to-enable-ldaps-for-your-aws-microsoft-ad-directory/) に則り各種設定を行う  

<img src="/images/2020/aws/ds/ad-ldap07.png" />  

複製後、証明書テンプレートから新規作成を選択し先程作成した ServerAuthentication を選択する  
<img src="/images/2020/aws/ds/ad-ldap08.png" />  
<img src="/images/2020/aws/ds/ad-ldap09.png" />  

### セキュリティグループ ACL の設定
テンプレートを作成すると Managed Microsoft AD 側が変更を受け取り証明書を交換し Ldap 連携を開始します  
そのための下位CA と AD のセキュリティグループを設定して互いに通信できるようにする  

* 下位CA からのインバウンド設定追加  

下位CA サーバのセキュリティグループのインバウンド通信に AD サーバ側のセキュリティグループからの通信を全て許可するようにする  

* AD 側セキュリティグループのアウトバウンド設定追加  

AD 側のセキュリティグループは AWS DirectoryService 上では確認出来ない  
セキュリティグループ管理画面等から AD の ID で検索すると出てくるのでそこでアウトバウンドに対して 下位CA サーバのセキュリティグループへの通信は全て許可するよう設定する  

<img src="/images/2020/aws/ds/ad-ldap10.png" />  

### AWS Microsoft ADがLDAPSを有効にする
上記までの手順が完了すると自動的に AWS DS 側で証明書の連携や Ldap の有効化が行われる  
最大でも 180 分以上かかるらしいので気長に待つ  

### LDAPS 連携確認
「サーバーマネージャ」のメニューバーから「ツール」より「ユーザーとコンピュータ」を選択  
下位CA サーバ等から Managed Microsoft AD のドメインコントローラーDNS名を確認し  

<img src="/images/2020/aws/ds/ad-ldap11.png" />  

ldap ツールから接続出来ることを確認できれば OK  


## ルートCA を利用した サーバ側 LDAP  
2020/06/02 確認出来たので追記  

基本的には 下位CA のときと同じで下記手順を実行するだけでいい  

* AD の管理者権限委任
* エンタープライズ CA にてルートCA構築
* 証明書テンプレートの作成
* セキュリティグループのACL設定追加

## よくわからないこと  
<i class="fas fa-external-link-alt"></i> [AWS Managed Microsoft AD を使用したサーバー側 LDAPS の有効化](https://docs.aws.amazon.com/ja_jp/directoryservice/latest/admin-guide/ms_ad_ldap_server_side.html)  

ここのマニュアルが簡素に書きすぎててワカラナイ  
また 証明書テンプレートを作成する項目で  

> [Enable Certificate Templates] ウィンドウで、[Kerberos Authentication] を選択して [OK] を選択します。これで AWS Microsoft AD ディレクトリのドメインコントローラーは、自動登録を通じて証明書を取得し、LDAPS を有効にすることができます。  

と **Kerberos Authentication** とあるが参考にした手順の記事と違う事が気になる  
また クライアントLdap 機能はAWSコンソールで証明書を登録したり視覚的にも有効化されていることが確認出来るが、サーバ側Ldapは特に何も項目が無いため管理上ワカラナイ  

最近 ClientVPN も SAML 連携ができるようになったりと AWS Directory Service はあまり注力、機能回収が行われる気がしないので  
正直本格利用するとなると AD サーバを時前で立てたほうが良いんじゃないかと考えてる人が多いのかもしれない  
※結局今回のような事をしようとすると ルートCA サーバ、下位CA サーバ立ててるわけだし  

ルートCA でも上記のように Ldap 連携を有効化できるそうなのでそちらも確認するかも  

---

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}