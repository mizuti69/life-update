+++
title = "よくみる OWASP Top10 の OWASP って何？"
tags = ["セキュリティ"]
categories = ["SE"]
date = "2019-09-24"
description = "セキュリティ製品等々でたまにみかける OWASP Top10対応ってなんのことなのか調べてみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## OWASPとは
AWS WAF のマネージドルールを見てたらよくよく **OWASP Top10対応** というのをよく見かけたんだけどなんのことなのか  

<i class="fas fa-external-link-alt"></i> [OWASP Japan](https://www.owasp.org/index.php/Japan)  

> OWASP - Open Web Application Security Project とは、Webをはじめとするソフトウェアのセキュリティ環境の現状、またセキュアなソフトウェア開発を促進する技術・プロセスに関する情報共有と普及啓発を目的としたプロフェッショナルの集まる、オープンソース・ソフトウェアコミュニティです。  
> The OWASP Foundationは、NPO団体として全世界のOWASPの活動を支えています。  

なるほど  
Webセキュリティ、脆弱性に対しての啓蒙活動からガイドラインやら要件定義資料やら診断ツールも提供してくれているとな  

## OWASP TOP10 2017
どんなものが世界の中でセキュリティリスクとして考えられているのか見てみる  
<i class="fas fa-external-link-alt"></i> [OWASP Top10 2017](https://www.owasp.org/images/2/23/OWASP_Top_10-2017%28ja%29.pdf)  

### A1:2017 インジェクション  

> SQLインジェクション、NoSQLインジェクション、OSコマンドインジェクション、LDAPインジェクションといったインジェクションに関する脆弱性は、コマンドやクエリの一部として信頼されないデータが送信される場合に発生します。  
> 攻撃コードはインタープリタを騙し、意図しないコマンドの実行や、権限を有していないデータへのアクセスを引き起こします。  

### A2:2017 認証の不備

> 認証やセッション管理に関連するアプリケーションの機能は、不適切に実装されていることがあります。  
> 不適切な実装により攻撃者は、パスワード、鍵、セッショントークンを侵害したり、他の実装上の欠陥により、一時的または永続的に他のユーザーの認証情報を取得します。  

### A3:2017 機微な情報の露出

> 多くのウェブアプリケーションやAPIでは、財務情報、健康情報や個人情報といった機微な情報を適切に保護していません。  
> 攻撃者は、このように適切に保護されていないデータを窃取または改ざんして、クレジットカード詐欺、個人情報の窃取やその他の犯罪を行う可能性があります。  
> 機微な情報は特別な措置を講じないでいると損なわれることでしょう。保存や送信する時に暗号化を施すことや、ブラウザ経由でやり取りを行う際には安全対策を講じることなどが必要です。  

### A4:2017 XML 外部エンティティ参照(XXE)

> 多くの古くて構成の悪いXMLプロセッサーにおいては、XML文書内の外部エンティティ参照を指定することができます。  
> 外部エンティティは、ファイルURIハンドラ、内部ファイル共有、内部ポートスキャン、リモートコード実行、DoS（サービス拒否）攻撃により、内部ファイルを漏えいさせます。  

### A5:2017 -アクセス制御の不備

> 権限があるもののみが許可されていることに関する制御が適切に実装されていないことがあります。  
> 攻撃者は、このタイプの脆弱性を悪用して、他のユーザのアカウントへのアクセス、機密ファイルの表示、他のユーザのデータの変更、アクセス権の変更など、権限のない機能やデータにアクセスします。  

### A6:2017 不適切なセキュリティ設定

> 不適切なセキュリティの設定は、最も一般的に見られる問題です。  
> これは通常、安全でないデフォルト設定、不完全またはアドホックな設定、公開されたクラウドストレージ、不適切な設定のHTTPヘッダ、機微な情報を含む冗長なエラーメッセージによりもたらされます。  
> すべてのオペレーティングシステム、フレームワーク、ライブラリ、アプリケーションを安全に設定するだけでなく、それらに適切なタイミングでパッチを当てることやアップグレードをすることが求められます。  

### A7:2017 クロスサイトスクリプティング(XSS)

> XSSの脆弱性は、適切なバリデーションやエスケープ処理を行っていない場合や、HTMLやJavaScriptを生成できるブラウザAPIを用いているユーザ入力データで既存のWebページを更新する場合に発生します。  
> XSSにより攻撃者は、被害者のブラウザでスクリプトを実行してユーザーセッションを乗っ取ったり、Webサイトを改ざんしたり、悪意のあるサイトにユーザーをリダイレクトします。  

### A8:2017 安全でないデシリアライゼーション

> 安全でないデシリアライゼーションは、リモートからのコード実行を誘発します。  
> デシリアライゼーションの欠陥によるリモートからのコード実行に至らない場合でさえ、リプレイ攻撃やインジェクション攻撃、権限昇格といった攻撃にこの脆弱性を用います。  

### A9:2017 既知の脆弱性のあるコンポーネントの使用  

> ライブラリ、フレームワークやその他ソフトウェアモジュールといったコンポーネントは、アプリケーションと同等の権限で動いています。  
> 脆弱性のあるコンポーネントが悪用されると、深刻な情報損失やサーバの乗っ取りにつながります。  
> 既知の脆弱性があるコンポーネントを利用しているアプリケーションやAPIは、アプリケーションの防御を損ない、様々な攻撃や悪影響を受けることになります。  

### A10:2017 不十分なロギングとモニタリング

> 不十分なロギングとモニタリングは、インシデントレスポンスに組み込まれていないか、非効率なインテグレーションになっていると、攻撃者がシステムをさらに攻撃したり、攻撃を継続できるようにし、ほかのシステムにも攻撃範囲を拡げ、データを改竄、破棄、破壊することを可能にします。  
> ほとんどのデータ侵害事件の調査によると、侵害を検知するのに200日以上も要しており、また内部機関のプロセスやモニタリングからではなく、外部機関によって検知されています。  

---
こうやって冒頭部分だけ見てみただけでもわかるように、何も特別で難しいことを言ってるわけじゃないんですね  
当たり前のことを当たり前に考慮しましょうという、そしてそれが一番大事で一番出来ていないってことなんですね  
難しい  

## セキュリティ診断ツールを使ってみよう  
幾つか脆弱性診断用のツールが提供されているので使ってみる  

### OWASP Zed Attack Proxy Project
<i class="fas fa-external-link-alt"></i> [https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)  

動作には Java は必要なためインストールしてない場合は端末に [ここ](https://www.oracle.com/technetwork/jp/java/javase/downloads/index.html) からインストールしておく  
OWASP ZAP のインストール  

<img src="/images/2019/se-owasp/owasp-zap01.png" />  
<img src="/images/2019/se-owasp/owasp-zap02.png" />  
<img src="/images/2019/se-owasp/owasp-zap03.png" />  
<img src="/images/2019/se-owasp/owasp-zap04.png" />  
<img src="/images/2019/se-owasp/owasp-zap05.png" />  

インストールが完了するとデスクトップにアイコンができてる  

<img src="/images/2019/se-owasp/owasp-zap06.png" />  

アプリの起動  

<img src="/images/2019/se-owasp/owasp-zap07.png" />  

メニューバーの「ツール」 -> 「オプション」から Proxy設定  
この辺は Jmeter と似てるなぁ  

<img src="/images/2019/se-owasp/owasp-zap08.png" />  

右上のアイコンから FireFox を起動し診断したいサイトの URL を入力する  

<img src="/images/2019/se-owasp/owasp-zap09.png" />  
<img src="/images/2019/se-owasp/owasp-zap10.png" />  

アクセスすると ZAP の 「サイト」に診断対象のサイトが登録される   
見てみるとサイト欄にアクセスした他ライブラリを参照している外部サイトも登録されてしまっているので、  
診断時に参照している外部サイトまで攻撃しないよう「プロテクトモード」に変更しておく  

<img src="/images/2019/se-owasp/owasp-zap11.png" />  

コンテキストグループに診断対象を登録  

<img src="/images/2019/se-owasp/owasp-zap12.png" />  

登録するとマークが変わる  

<img src="/images/2019/se-owasp/owasp-zap13.png" />  

これで診断の準備が整ったため実際に診断してみる  
**診断を行う際には自分で管理しているサイト以外には行わないようにしましょう**  
**最悪の場合悪意ある攻撃とみなされなんらかの法的措置を取られる可能性があります**  

<img src="/images/2019/se-owasp/owasp-zap14.png" />  
<img src="/images/2019/se-owasp/owasp-zap15.png" />  

診断結果の確認  
今回は検証なので単純に静的サイトを診断してみましたが、ちゃんとよく言われるような `X-Frame-Optionsヘッダー` や `XSS` 対策 `X-Content-Type-Options` のアラートが出てますね  

<img src="/images/2019/se-owasp/owasp-zap16.png" />  

スキャン時に指定したスキャンポリシーので default って何見てるんだろと見てみると、大体 XSS や インジェクション系だね  

<img src="/images/2019/se-owasp/owasp-zap17.png" />  

### OWASP OWTF
<i class="fas fa-external-link-alt"></i> [https://www.owasp.org/index.php/OWASP_OWTF](https://www.owasp.org/index.php/OWASP_OWTF)  
<i class="fas fa-external-link-alt"></i> [Welcome to Offensive Web Testing Framework’s documentation!](https://owtf.readthedocs.io/en/develop/index.html)  

> OWASP Testing GuideやOWSP Top 10、PTES(Penetration Testing Execution Standard)などのセキュリティ基準に沿った効率的な検査をするためのペネトレーション検査ツール

Python と Postgresql が必要でちょっと手間掛かりそうなのでスキップ  
Docker イメージも提供してるらしいけど  

### OWASP Xenotix XSS Exploit Framework
<i class="fas fa-external-link-alt"></i> [https://www.owasp.org/index.php/OWASP_Xenotix_XSS_Exploit_Framework](https://www.owasp.org/index.php/OWASP_Xenotix_XSS_Exploit_Framework)  
残念ながら開発は中止しているらしい  

---

他にもセキュリティ要件定義書とかみてもそこまで細かくなくとっつきやすそう  
書いてある内容は当たり前的なことが多いためセキュリティって何考えればいいの？お客様に指標を出せって言われちゃったけど何を出せばいいかな？  
とかのときには使えそうだ  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/B01J9E0HCI/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/51Qg2V%2BtsYL._SL160_.jpg" alt="Webセキュリティ担当者のための脆弱性診断スタートガイド 上野宣が教える情報漏えいを防ぐ技術" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
