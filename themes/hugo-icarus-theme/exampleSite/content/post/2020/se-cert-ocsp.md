+++
title = "「サーバーの証明書失効を確認する」とは"
tags = ["セキュリティ","ブラウザ"]
categories = ["SE"]
date = "2020-03-16"
description = "Internet Explorer を使っているときに出た「このサイトのセキュリティ証明書の取り消し情報は、使用できません。続行しますか」について"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## このサイトのセキュリティ証明書の取り消し情報は、使用できません。続行しますか
IEなんて使ってる人まだいるんだね、いるよ私だよ  
会社の内部システムとか未だに IE じゃないと上手く動かないなんて会社はごマンとあるでしょう  

あるとき普通にインターネットに出ているサイトだけどアクセスしたら下記のようなポップアップが  

<img src="/images/2020/se-cert-ocsp/ocsp-01.jpg" />  

Chrome、Edge、Firefox では表示されない  
しかもURLのバーについては緑色になっていて証明書エラーが起きているわけではない  

なんか証明書に問題があるんだろうと「証明書の表示」を見てみる  

<img src="/images/2020/se-cert-ocsp/ocsp-02.png" />  

しらねぇサイトだ  
どうやらサイト内で利用している JS 等から読み出しているサイト側の証明書に問題があるようだ  

## 問題調査

### IE でメッセージが現れる理由について
なぜ IE でだけこのポップアップが出てくるのか  
IE の下記 **「サーバの証明書失効を確認する」** 設定が有効になっていた場合、SSL証明書の有効性を確認するためそこで問題が起きるとこのようなポップアップが出てくる  
ポップアップ消せればいい人はここで設定変更して解決で終わりでいい  

<img src="/images/2020/se-cert-ocsp/ocsp-03.png" />  

でもここで疑問「この処理自体は必要なものだし他のブラウザもやってるんじゃないのか？」  

### SSL証明書の有効性チェック  
IE だけの問題なのか、 SSLLabs でチェックしてみた  
<i class="fas fa-external-link-alt"></i> [SSL Labs](https://www.ssllabs.com/ssltest/)  

<img src="/images/2020/se-cert-ocsp/ocsp-04.png" />  

ちゃんと？エラーが出ている、つまりブラウザの問題ではなく CA側か証明書に問題がある  

### OCSPとは？  
そもそもSSL証明書の有効性確認ってどうやっているのか  
デジタル証明書が失効していないものなのかをチェックする方法は大きく2つの方法がある  
<i class="fas fa-external-link-alt"></i> [SSL ハンドシェイクの裏側](https://www.digicert.co.jp/welcome/pdf/wp_ssl_handshake.pdf)  

**<i class="fas fa-chevron-circle-right"></i> CRL**  
デジタル証明書とCRLを照合することで証明書が現在も有効であるかを確認できる  
CRLは認証局（CA）から定期的に最新情報が配布されるので、それをダウンロードする  
クライアント視点から解説すると、サーバ証明書の有効性をチェックする場合、Webサイトなどから受信したサーバ証明書のシリアル番号とCRLに登録された証明書のシリアル番号を照合して有効性を確認する  

証明書の シリアル番号と ローカルの CRL を突合させるわけだがこのやり方ではリアルタイム性が無い  
そこでオンラインにしようというのが OCSP  

**<i class="fas fa-chevron-circle-right"></i> OCSP**  
ウェブブラウザは http のパケットに証明書ステータス確認要求（以下、OCSP リクエスト）を追加して、  
オンラインで OCSP サーバ（OCSP レスポンダ）に要求します  
OCSP レスポンダは、証明書のステータスを“good”（失効していない）、“revoked”（失効している）、“unknown”（不明）のいずれかに分類し応答 ( 以下、OCSP レスポンス ) を返します  

OCSP レスポンダが参照する失効情報データは、CRL を参照している場合もあれば、認証局のデータベースを直接参照していることもあり認証局によって異なります  
CRL を参照している場合、OCSP レスポンスのデータの“鮮度”は CRLと同等となります  
Baseline Requirements では、認証局は少なくとも 4 日毎に OCSP 経由で提供される情報を更新するものとする（SHALL）  
このサービスからの OCSP レスポンスの有効期間は 10 日以下でなければならない（MUST）、としています  

<img src="/images/2020/se-cert-ocsp/ocsp-05.png" />  

証明書を見てみるとこのように発行機関と参照するべき OCSP 情報が乗っている  
OCSPは CRLと違って CA側が管理提供しているため、参照エラーが出ている以上 SSL証明書が間違ってなければ OCSP 側、 CA 側に問題が起きてるのかな？  

## なぜ他のブラウザではエラー出ないのか？  
ここまで来て IE 固有の問題では無さそうなのに chromium 系 と firefox では問題が起きていない  
実はチェックしてないのか？  

**<i class="fas fa-chevron-circle-right"></i> Chrome系の場合**  
<i class="fas fa-external-link-alt"></i> [CRLSets](https://dev.chromium.org/Home/chromium-security/crlsets)  
Chromium系の場合、OCSP、CRLは使わず各認証局の発行するCRL情報をまとめて軽量化した CRLSets という独自に実装された仕組みを利用する  
そのため Chromium 系のブラウザ GoogleChrome と Edge では何も起きなかったのか（Edgeよそれで良いのか？）  
CRLSets側で OCSP 側に問題が起きていた場合どうするかはわからない  

**<i class="fas fa-chevron-circle-right"></i> Firefox系の場合**  
FireFoxも IE 同様に 証明書のAIA を見て OCSP に参照しにいくらしい  
ん？でもエラー出なかったけど？  
Firefoxの場合下記オプションの設定にチェックするかしないかの項目があり、ここがチェックついてることを確認してもエラーが出なかった...  

<img src="/images/2020/se-cert-ocsp/ocsp-06.png" />  

よくわからん  


---

こんな感じで調べていると、OCSPサーバに証明書更新時の反映が遅れてエラーになった～みたいな記事もあったり  
CRLの動きやブラウザ依存というのもあってなかなか原因をというのは難しいようだ  
CA側やサイト管理者側がニュースリリースとかでも出してくれない限りユーザー側からは何か問題おきてんなーくらいしかわからない  

見る、という目的だけで考えれば Chrome の方がストレスはないんだけど  
正しい、という意味では今回の IE の動きは正しく（第三者的に診断サービス使ってもエラーだったし）他のブラウザは適当だなーと感じた  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4908686009/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/41y5jGy1GrL._SL160_.jpg" alt="プロフェッショナルSSL/TLS" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
