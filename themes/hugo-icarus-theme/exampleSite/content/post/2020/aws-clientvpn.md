+++
title = "AWS ClientVPN + AD相互認証①"
tags = ["aws"]
categories = ["SE"]
date = "2020-03-30"
description = "AWS ClientVPN検証"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS ClientVPN
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/vpn/" data-iframely-url="//cdn.iframe.ly/Xo6etMC?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金

> AWS Client VPN endpoint association	0.15USD per hour  
> AWS Client VPN connection	0.05USD per hour  

との事なので、基本利用量＋接続重量＋転送量かな  

## AWS DirectoryService
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/directoryservice/" data-iframely-url="//cdn.iframe.ly/6jsKxGN?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金  
> **AWS Directory Service for Microsoft Active Directory (Standard Edition)	時間料金総額**  
> 基本料金  
>   可用性を向上させるために 、2 つのドメインコントローラーが含まれています。  
>   ドメインコントローラーごとに 1 時間あたり 0.073 USD の時間料金が AWS により請求されます。  
>   0.146 USD  
> 
> ドメインコントローラーを 1 つ追加するごと	0.073 USD  
> ディレクトリを共有する追加のアカウントごとの料金	0.0219 USD  
> 
> **AWS Directory Service for Microsoft Active Directory (Enterprise Edition)	時間料金総額**  
> 基本料金  
>   可用性を向上させるために 、2 つのドメインコントローラーが含まれています。  
>   ドメインコントローラーごとに 1 時間あたり 0.2225 USD の時間料金が AWS により請求されます。  
>   0.445 USD  
> 
> ドメインコントローラーを 1 つ追加するごと	0.2225 USD  
> ディレクトリを共有する追加のアカウントごとの料金	0.06675 USD  

結構複雑。基本利用料金＋コントローラー数＋エディション料金＋オブジェクト数  
みたいなイメージかな？  
上記はMicrosoftADの料金計算で、SimpleADはsambaベースでもうちょっと安い  

## 環境構築  

### VPCとサブネット  
割愛するが、
NAT Gateway を配置するパブリック用 DMZ側サブネットを1つ、  
VPNエンドポイントや ADと紐付ける Trust側サブネットを2つ用意する  

<img src="/images/2020/aws/clientvpn/clientvpn-01.png" width="60%" />  

マルチAZする場合は NATGatewayを2個建てしないとか・・・  

### VPN用証明書の作成
AWS ClientVPN では OpenVPN で利用するサーバ証明書とクライアント証明書認証かAD認証、及び組み合わせた相互認証を選択できます  
AD認証がない場合クライアント証明書があれば誰でも接続出来てしまうので  
ADと接続してユーザーID/PASS認証できるよう相互認証する  

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/certificate-manager/" data-iframely-url="//cdn.iframe.ly/8uyXWEL?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

> 月/リージョンで作成されたプライベート証明書の数1～1,000	0.75USD  

証明書の管理には ACMを使う  
ACM上でプライベートCAを作成、管理すると料金がかかるため自作する  
別に公開利用するわけじゃないしね  

<i class="fas fa-external-link-alt"></i> [クライアント認証と認可](https://docs.aws.amazon.com/ja_jp/vpn/latest/clientvpn-admin/authentication-authrization.html)  

マニュアルを参考に適当な Linux サーバ上で証明書を作成  

```
# git clone https://github.com/OpenVPN/easy-rsa.git
# cd easy-rsa/
# cd easyrsa3/
# ./easyrsa init-pki

init-pki complete; you may now create a CA or requests.
Your newly created PKI dir is: /usr/local/src/easy-rsa/easyrsa3/pki

# ./easyrsa build-ca nopass
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017
Generating RSA private key, 2048 bit long modulus
.......................................................................................................................................................................+++
............+++
e is 65537 (0x10001)
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Common Name (eg: your user, host, or server name) [Easy-RSA CA]:

CA creation complete and you may now import and sign cert requests.
Your new CA certificate file for publishing is at:
/usr/local/src/easy-rsa/easyrsa3/pki/ca.crt


# ./easyrsa build-server-full server nopass
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017
Generating a 2048 bit RSA private key
...............................................................................+++
.........+++
writing new private key to '/usr/local/src/easy-rsa/easyrsa3/pki/easy-rsa-31525.yWONb3/tmp.tmlLVm'
-----
Using configuration from /usr/local/src/easy-rsa/easyrsa3/pki/easy-rsa-31525.yWONb3/tmp.vmKzdy
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName            :ASN.1 12:'server'
Certificate is to be certified until Jun 29 09:56:58 2022 GMT (825 days)

Write out database with 1 new entries
Data Base Updated


# ./easyrsa build-client-full client1.domain.tld nopass
Using SSL: openssl OpenSSL 1.0.2k-fips  26 Jan 2017
Generating a 2048 bit RSA private key
..+++
....................................+++
writing new private key to '/usr/local/src/easy-rsa/easyrsa3/pki/easy-rsa-31645.E8ek4r/tmp.QNxYLL'
-----
Using configuration from /usr/local/src/easy-rsa/easyrsa3/pki/easy-rsa-31645.E8ek4r/tmp.4qkqYT
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName            :ASN.1 12:'client1.domain.tld'
Certificate is to be certified until Jun 29 09:57:39 2022 GMT (825 days)

Write out database with 1 new entries
Data Base Updated

```

デフォルトで作成すると証明書の有効期限は2年  

| 証明書 | ファイル |
| :-- | :-- |
| CA | pki/ca.crt |
| サーバ証明書 | pki/issued/server.crt |
| クライアント証明書 | pki/issued/client1.domain.tld.crt |
| CA 鍵 | pki/private/ca.key |
| サーバ証明書鍵 | pki/private/server.key |
| クライアント証明書鍵 | pki/private/client1.domain.tld.key |

### 証明書をACMにインポート
AWS ACM で作成したクライアント証明書以外をインポートする  
上から 証明書、証明書鍵、CAの内容を貼り付けて登録する  

<img src="/images/2020/aws/clientvpn/clientvpn-02.png" />  
<img src="/images/2020/aws/clientvpn/clientvpn-03.png" />  

### DirectoryServerの作成
ClientVPNで参照するADを作成する  

<img src="/images/2020/aws/clientvpn/ad-01.png" />  
<img src="/images/2020/aws/clientvpn/ad-02.png" />  
<img src="/images/2020/aws/clientvpn/ad-03.png" />  
<img src="/images/2020/aws/clientvpn/ad-04.png" />  

管理者のID/PASSは絶対忘れないように  
あとは用途に応じてサイズやVPCを選択する  

SimpleADの場合ログ的なものは取得できない（多分）ので用途に応じて利用するADを選択する  
ADのマネージドサービスだが、オブジェクトの管理はコンソールやAPIで出来ないので別途手法を用意する必要がある  

### VPNエンドポイントの作成
<i class="fas fa-external-link-alt"></i> [クライアント VPN エンドポイント](https://docs.aws.amazon.com/ja_jp/vpn/latest/clientvpn-admin/cvpn-working-endpoints.html)  

VPCのカテゴリーから作成する  

<img src="/images/2020/aws/clientvpn/clientvpn-04.png" />  

クライアント IPv4 CIDR はクライアントに払い出されるローカルIP  
最低でもサブネットマスク「/22」以上である必要がある  

事前に設定しておいた ADや証明書はここで指定  

<img src="/images/2020/aws/clientvpn/clientvpn-05.png" />  

ログ出力する場合はロググループだけ指定してあげれば自動で出してくれる  

<img src="/images/2020/aws/clientvpn/clientvpn-06.png" />  

スプリットトンネルを有効化すると必要な通信（ルーティング情報に基づき）VPN経路と通常ネットワークと分けられる  
VPN経由の通信に制限したい場合はチェックを外す  

以上で作成すると「関連付け中・・・」みたいな感じでまだ利用できない  

### VPNのサブネット関連付け  
VPN経由でアクセスさせるVPC、サブネットを関連付け  

<img src="/images/2020/aws/clientvpn/clientvpn-07.png" />  

### 認証許可設定の関連付け
アクセスを許可するネットワーク帯を関連付け  

<img src="/images/2020/aws/clientvpn/clientvpn-08.png" />  
<img src="/images/2020/aws/clientvpn/clientvpn-09.png" />  

そうして少し待つと状態が「使用可能」となる  

## クライアント設定

### OpenVPN、クライアントのインストール  
<i class="fas fa-external-link-alt"></i> [OpenVPNクライアント](https://www.openvpn.jp/download/)  
<i class="fas fa-external-link-alt"></i> [AWS Client VPN download](https://aws.amazon.com/jp/vpn/client-vpn-download/)  

両方インストールする必要がある  
OpenVPNクライアントをインストールするとアダプター等PCの周りを設定してくれる  
AWS Client はそのへんをやってくれないので注意  

例えば下記のように  
なんか接続してみてもすぐ切れてしまう  
AWS側のログ見てもNAになっててわからないとなり違う、詳細ログが出る OpenVPNクライアントで接続テストしてみると下記のようなエラーだった  

```
There are no TAP-Windows adapters on this system.  You should be able to create a TAP-Windows adapter by going to Start -> All Programs -> TAP-Windows -> Utilities -> Add a new TAP-Windows virtual ethernet adapter.
```

またOpenVPNクライアントのダウンロード画面にある VpnuxClient もただのクライアント機能しかないので注意  

### VPNの設定
AWS クライアントVPNの画面にある「クライアント設定のダウンロード」から  
AWS VPNクライアントソフトにインポートするようの設定ファイルをダウンロードする  

* クライアント証明書  
* クライアント証明書鍵  
* VPNクライアント  
* VPN設定ファイル  

上記をVPN接続ずる端末に用意し適当な場所に保存する  
VPN設定ファイルをテキストエディタで開き、クライアント証明書情報をファイル末尾に記述する  

```
cert C:\\xxxxxxx\\aws-vpn_cert\\client1.crt
key C:\\xxxxxxx\\aws-vpn_cert\\client1.key
```

追記後、ファイルをインポート  

<img src="/images/2020/aws/clientvpn/clientvpn-10.png" />  

設定後、接続を行えば繋がる  

## その他  

### CloudWatchのログ  
接続に成功すると下記のようなログが出る  
接続元IP、認証したユーザー名、日時などがちゃんと出てるのは嬉しい  

```
{
    "connection-log-type": "connection-attempt",
    "connection-attempt-status": "successful",
    "connection-attempt-failure-reason": "NA",
    "connection-id": "cvpn-connection-*****************",
    "client-vpn-endpoint-id": "cvpn-endpoint-*****************",
    "transport-protocol": "udp",
    "connection-start-time": "2020-03-26 13:07:13",
    "connection-last-update-time": "2020-03-26 13:07:13",
    "common-name": "client1.domain.tld",
    "username": "testuser",
    "device-type": "win",
    "device-ip": "*****************",
    "port": "36919",
    "ingress-bytes": "0",
    "egress-bytes": "0",
    "ingress-packets": "0",
    "egress-packets": "0",
    "client-ip": "192.168.157.130",
    "connection-end-time": "NA"
}
```

### VPNエンドポイントの制限  

* アカウントあたりの クライアント VPN エンドポイント数: 5  
* クライアント VPN エンドポイントあたりの承認ルールの数: 50  
* クライアント VPN エンドポイントあたりのルート数: 10  
* クライアント VPN エンドポイントあたりの同時クライアント接続数: 2000  
* 1つの Client VPN Endpoint に複数のクライアント証明書を登録することはできない  

最大 1VPCあたり1万人くらい接続できそう  
回線速度について明言は無いけど 100Mbps～1Gbps くらいっぽい  
気をつけなければいけないこと  

* クライアント証明書、サーバ証明書は自動更新では無い  
* ACMに登録したCAから払い出されたクライアント証明書であればACMに登録してなくても認証できる  
* クライアント証明書の失効管理はできる  

---

大分長くなったので NAT Gateway と DirectoryService のセットアップは次回に続く  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}