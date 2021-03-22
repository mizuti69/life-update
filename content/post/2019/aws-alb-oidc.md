+++
title = "ALB + Goolge認証 でアクセス制限をかける"
tags = ["aws"]
categories = ["SE"]
date = "2019-08-23"
description = "社内サイトに対して社外でもアクセスさせたいけど社員以外はアクセスできないようにしたいので ALB + Google認証できないか検証"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Google API の準備
Google APIs で認証情報を作成する  
<i class="fas fa-external-link-alt"></i> [Google APIs](https://console.developers.google.com/)  

<img src="/images/2019/aws-alb-oidc/oidc_setup01.png" />  

ALB が GoogleAPI と認証のやり取りをするリダイレクトURLは ALB 指定のものを設定しておく  

<img src="/images/2019/aws-alb-oidc/oidc_setup02.png" />  

Google OpenID Provider のエンドポイント情報を確認しておく  

```
$ curl https://accounts.google.com/.well-known/openid-configuration
{
 "issuer": "https://accounts.google.com",
 "authorization_endpoint": "https://accounts.google.com/o/oauth2/v2/auth",
 "token_endpoint": "https://oauth2.googleapis.com/token",
 "userinfo_endpoint": "https://openidconnect.googleapis.com/v1/userinfo",
 "revocation_endpoint": "https://oauth2.googleapis.com/revoke",
 "jwks_uri": "https://www.googleapis.com/oauth2/v3/certs",
~

```

## ALB にて認証設定
### OIDC設定
認証をかけたいメンバーグループのルールに認証フローを追加する  

<img src="/images/2019/aws-alb-oidc/oidc_setup03.png" />  
<img src="/images/2019/aws-alb-oidc/oidc_setup04.png" />  

追加後、設定したURLにアクセスしてみると  
Google認証が来て、ログインすると通常の画面が表示される

<img src="/images/2019/aws-alb-oidc/oidc_setup05.png" />  

### Googleアカウント制限
このままでは Google アカウントを持っていれば誰でもアクセスできてしまうため、
特定の企業アカウントのみログインできるように制御する  

ALBにて認証ルールの追加リクエストパラメータに `hd` 属性を指定する  

<img src="/images/2019/aws-alb-oidc/oidc_setup06.png" />  

他にどのようなパラメータが使用可能かは GooglAPI のドキュメントに記載がある  
<i class="fas fa-external-link-alt"></i> [OpenID Connect Authentication URI parameters](developers.google.com/identity/protocols/OpenIDConnect#authenticationuriparameters)  


{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
