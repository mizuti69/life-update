+++
title = "AWS ClientVPN + AD相互認証②"
tags = ["aws"]
categories = ["SE"]
date = "2020-04-07"
description = "AWS ClientVPN検証"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++


<!--more-->

前回 [>> AWS ClientVPN + AD相互認証](/2020/03/30/aws-clientvpn/) の続き  

## NAT Gateway の準備
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/vpc/" data-iframely-url="//cdn.iframe.ly/3eqo7co?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金  
基本利用料金＋転送量  

> Price per NAT gateway ($/hour) 0.062USD  

> Price per GB data processed ($) 0.062USD  

### NAT Gatewayの作成
<img src="/images/2020/aws/clientvpn/clientvpn-01.png" width="60%" />  

NAT Gateway用のパブリックサブネットを用意し、そこに作成する  

<img src="/images/2020/aws/clientvpn/ng-01.png" />  

NAT Gateway用のルートテーブルを作成し  

<img src="/images/2020/aws/clientvpn/ng-02.png" />  

クライアントVPNで設定したサブネットは NAT Gateway 経由で出ていくようサブネットを紐付け  

<img src="/images/2020/aws/clientvpn/ng-03.png" />  
<img src="/images/2020/aws/clientvpn/ng-04.png" />  

デフォルト通信は NAT Gateway で出ていくようルーティング設定  

<img src="/images/2020/aws/clientvpn/ng-05.png" />  

## AD管理用 Windowsサーバの準備
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/ec2/" data-iframely-url="//cdn.iframe.ly/JsmuONw?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

### 料金
ADのアカウント作成・管理用に WindowsServer を用意する  
※ SimpleAD の場合バックは Samdba だが Ldap や Samdba で直接連携はできるのか？情報がなさすぎる  

<i class="fas fa-external-link-alt"></i> [AWS Pricing Calculator](https://calculator.aws/#/)  
Windows Serverなので t3.medium で立てたらだいたい $53 くらい  

### インスタンスの作成  
特に特別な手順でも無いので割愛  
注意点としては可能であれば **Japanise** 対応しているOSを最初から選んでおくと楽  
作成時に AD を参照させておくこと  

<img src="/images/2020/aws/clientvpn/ad-05.png" />  

Windowsサーバーの場合、初回ログイン情報はコンソール上から発行する必要がある  

<img src="/images/2020/aws/clientvpn/ad-06.png" />  

## 運用を考える  

### クライアント証明書の管理  
自前 CA を立てるのであれば証明書の有効期限はある程度融通がきく  
easy-rsa を使っている場合変数で定義されている  

```
# cd easy-rsa/easyrsa3/
# cp -p vars.example vars
# vim vars
~

#set_var EASYRSA_CA_EXPIRE      3650  # CAの有効期限
set_var EASYRSA_CERT_EXPIRE     365   # サーバ、クライアント証明書の有効期限

```

それぞれ証明書コマンド実行時に上記変数が参照されるので、  
サーバ証明書は10年、クライアント証明書は1年と調整が出来る  

証明書の更新タイミングの配布方法等は検討が必要  
接続状況や監査系は クライアントVPN のログと VPCトラフィックログで取ろう  

証明書を配布する以外にも、コンフィグファイルに直接記載する方法もある  
<i class="fas fa-external-link-alt"></i> [iPhoneやiPad (iOS) でOpenVPNを使ってみよう！](https://www.openvpn.jp/document/ios-openvpn/)  

```
 *省略
cipher AES-256-GCM
verb 3
<ca>
-----BEGIN CERTIFICATE-----
 *省略
-----END CERTIFICATE-----
</ca>
<cert>
-----BEGIN CERTIFICATE-----
 *省略
-----END CERTIFICATE-----
</cert>
<key>
-----BEGIN PRIVATE KEY-----
 *省略
-----END PRIVATE KEY-----
</key>
auth-user-pass
reneg-sec 0
```

スマートフォン等から接続したい場合は上記のように記載してコンフィグをインポートする  
またVPN接続先のホスト名が設定ファイル上は下記のようになっていて  

```
remote cvpn-endpoint-**************.prod.clientvpn.ap-northeast-1.amazonaws.com 1194
remote-random-hostname
```

そのままOpenVPNクライアントでインポートするとホスト名が名前解決できずに失敗する  
ClientVPN の接続先ドメイン名は `*.cvpn-endpoint-**************.prod.clientvpn.ap-northeast-1.amazonaws.com` とアスタリスクで登録されているため  
名前解決できるよう適当なサブドメインも証明書情報と合わせて追記いておく  

```
remote vpn-test.cvpn-endpoint-**************.prod.clientvpn.ap-northeast-1.amazonaws.com 1194
remote-random-hostname
```

※クライアントソフトにより `*` アスタリスクサブドメインは受け入れていない  

### AD アカウント管理
WindowsServerはユーザー管理作業時のみ起動するように利用すれば金額自体は対して課題にはならない  
ユーザーの一括登録や一括削除は別途処理を考えたほうがよさそう  

SimpleAD ではなく、 Microsoft AD にすれば AD側の監査ログも取れる  

### NAT Gateway
エンドポイントは 1サブネットにしか関連付けられないため、冗長性等も考えスタンバイ用に 違うサブネットに予備の NAT Gateway を立てとくのも有りかもしれない  

### 後から変更が出来ないポイント
* クライアントVPNが参照するADサーバ  
* 各サービスのVPC、サブネット設定  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
