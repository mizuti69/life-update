+++
title = "IoT 機器のセキュリティと運用について調査"
tags = ["IoT","セキュリティ"]
categories = ["SE"]
date = "2019-11-13"
description = "IoT 機器の監視や運用ってどうしているのか、どうするべきなのかの調査"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## セキュリティ

### 経済産業省
<i class="fas fa-external-link-alt"></i> [IoTセキュリティガイドライン](https://www.meti.go.jp/press/2016/07/20160705002/20160705002.html)  
<i class="fas fa-external-link-alt"></i> [産業保安版のデータ契約ガイドライン及びセキュリティマニュアル](https://www.meti.go.jp/policy/safety_security/industrial_safety/oshirase/2019/4/20190425.html)  

お役所のガイドラインから一部気になった点を抜粋する  

<i class="fas fa-chevron-circle-right"></i> IoTセキュリティ要因の理解  
IoTを扱う場合においては下記のような特性を理解しセキュリティ対策をする必要があるとのこと  

* IoT 機器のライフサイクルが長いこと  
* IoT 機器に対する監視が行き届きにくいこと  
* IoT 機器側とネットワーク側の環境や特性の相互理解が不十分であること  
* IoT 機器の機能・性能が限られていること  
* 開発者が想定していなかった接続が行われる可能性があること  

<i class="fas fa-chevron-circle-right"></i> 守るべきものを特定する  
**本来機能の洗い出し**  
IoT 機器・システムが有する本来機能（自動車であれば「走る」、「曲がる」、「止まる」、エアコンであれば冷暖房といった機能）、生成されるセンサーデータ、ログ等の情報を洗い出す。  
遠隔操作など、つながりを利用した  機能が追加されたり、その機能のために情報を生成したりするケースも想定されるため、ネットワークの設定情報等ネットワークに関係する事項も含め洗い出す。  

**情報の洗い出し**  
IoT 機器・システムが収集するセンサーデータや個人情報（プライバシー含む）、所有する設計情報などの技術情報を洗い出す。  
また、機能を構成するソフトウェアやその設定情報も読み出されて攻撃手法の考案に利用されたり、改ざんされて不正操作されるリスクがあるため、守るべきものとして洗い出す。  

* コンテンツ  
  音声、画像、動画等のマルチメディアデータ、コンテンツ利用履歴等  
* ユーザ情報  
  ユーザの個人情報、ユーザ認証情報、利用履歴・操作履歴、GPS で取得した位置情報等  
* 機器情報  
  情報家電そのものに関する情報、機器認証情報等  
* ソフトウェアの状態情報  
  各ソフトウェアに固有の状態情報  
* ソフトウェアの設定情報  
  各ソフトウェアに固有の設定情報  
* ソフトウェア  
  OS、ミドルウェア、アプリケーシン、ファームウェア等  
* 設計情報、内部ロジック  
  仕様・設計等の設計情報であり、ソフトウェアの解析や動作時に発する電磁波等から読み取られるロジック  

<i class="fas fa-chevron-circle-right"></i> つながりで波及するリスクを想定する  
IoT では機器やシステムに故障が発生したり、ウイルスに感染したりした場合に、つながりを通じて影響が広範囲に伝播することが懸念される。  
機能停止すれば連携する機器やシステムに影響を与えるし、ウイルス感染で踏み台にされれば被害者から加害者に転じることとなる。  
機器やシステムが自分自身の異常状態や他の機器を攻撃していることを認識できない場合もありうる。 

<i class="fas fa-chevron-circle-right"></i> 物理的なリスクを認識する  
IoT では、持ち歩いたり、家庭や公共空間などに設置された機器やシステムも IoT を構成するようになる。  
このため、盗まれたり紛失した機器が不正操作されたり、駐車場や庭、公共空間に設置された機器が第三者によって物理的に攻撃される危険性がある。  
また、廃棄した機器から情報が漏えいしたり、不正なソフトウェアを組み込んだ機器が中古販売される可能性もある。  

<i class="fas fa-chevron-circle-right"></i> 対策例  
**ハードウェアによる対策**  

* 不要な非正規 I/F や露出した配線の除去  
*  専用認証デバイスを接続しないと内部にアクセスできない設計  
* チップや配線の内装化  
* 不正な埋め込みや品質上の問題がないことを確認  
* 外部の信頼できるシステムを利用した定期的な時刻補正  

**ソフトウェアによる対策**  

* 盗難、紛失時に遠隔から端末をロックする機能の実装  
* 機密データの暗号化、使用時のメモリなど在中時間の短縮  
* 利用者認証  
* メッセージデータの正当性検証  
* ファジングツール等による脆弱性対策  
* ロギング  
* ソフトウェアのアップデート  

**ネットワーク設計による対策**  

* IoT 機器・システムを構成する機器やシステムを物理的または仮想的なゲートウェイにより複数の領域（以下「ドメイン」）に分割し、異常発生の影響の範囲を局所化したり、重要な機能を多重のゲートウェイにより守る  
* IoT機器・システムが通信でつながる接点を考慮するとともにゲートウェイを設け、攻撃を遮断する  
* 監視機能を有する他の IoT 機器・システムにより、機器やシステムを監視し異常検知や原因推定を行う  
* 暗号機能の適用  
* 第三者適合性評価制度、ISMS 適合性評価制度等  

### OWSAP IoT TOP10
<i class="fas fa-external-link-alt"></i> [OWASP Internet of Things Project ](https://www.owasp.org/index.php/OWASP_Internet_of_Things_Project)  

以下に内容を抜粋する  

<i class="fas fa-chevron-circle-right"></i> 1. Weak Guessable, or Hardcoded Passwords  
Use of easily bruteforced, publicly available, or unchangeable credentials, including  
backdoors in firmware or client software that grants unauthorized access to deployed  
systems.  

安易な資格情報、認証情報を使うなよ  

<i class="fas fa-chevron-circle-right"></i> 2. Insecure Network Services  
Unneeded or insecure network services running on the device itself, especially those  
exposed to the internet, that compromise the confidentiality, integrity/authenticity, or  
availability of information or allow unauthorized remote control…  

安全で無いネットワークに接続すんなよ  

<i class="fas fa-chevron-circle-right"></i> 3. Insecure Ecosystem Interfaces  
Insecure web, backend API, cloud, or mobile interfaces in the ecosystem outside of the  
device that allows compromise of the device or its related components. Common issues  
include a lack of authentication/authorization, lacking or weak encryption, and a lack of  
input and output filtering.  

デバイスへのアクセス、バックエンドとのアクセスにおいて  
認証、暗号化、フィルタリングなどをちゃんとやれよ  

<i class="fas fa-chevron-circle-right"></i> 4. Lack of Secure Update Mechanism  
Lack of ability to securely update the device. This includes lack of firmware validation on  
device, lack of secure delivery (un-encrypted in transit), lack of anti-rollback mechanisms,  
and lack of notifications of security changes due to updates.  

デバイスのアプリケーション、ファームウェアを安全に更新、検証、ロールバックできる機構の構築  

<i class="fas fa-chevron-circle-right"></i> 5. Use of Insecure or Outdated Components  
Use of deprecated or insecure software components/libraries that could allow the device to be  
compromised. This includes insecure customization of operating system platforms, and  
the use of third-party software or hardware components from a compromised supply chain.  

古く脆弱なソフトウェアやファームウェア、ライブラリを使用し続けるなよ  

<i class="fas fa-chevron-circle-right"></i> 6. Insufficient Privacy Protection  
User’s personal information stored on the device or in the ecosystem that is used insecurely,  
improperly, or without permission.  

利用者情報がデバイス上に保存され続けるなど取得した情報に対して安全に取り扱われているか、許可を得ているか  

<i class="fas fa-chevron-circle-right"></i> 7. Insecure Data Transfer and Storage  
Lack of encryption or access control of sensitive data anywhere within the ecosystem,  
including at rest, in transit, or during processing.  

折り扱うデータ、通信は暗号化やアクセス制御を必ず行えよ  

<i class="fas fa-chevron-circle-right"></i> 8. Lack of Device Management  
Lack of security support on devices deployed in production, including asset management,  
update management, secure decommissioning, systems monitoring, and response  
capabilities.  

資産管理、更新管理、安全な廃棄、システム監視、レスポンス機能など管理・監視・運用をちゃんとやれよ  

<i class="fas fa-chevron-circle-right"></i> 9. Insecure Default Settings  
Devices or systems shipped with insecure default settings or lack the ability to make the  
system more secure by restricting operators from modifying configurations.  

初期設定は使うな  

<i class="fas fa-chevron-circle-right"></i> 10. Lack of Physical Hardening  
Lack of physical hardening measures, allowing potential attackers to gain sensitive  
information that can help in a future remote attack or take local control of the device.  

物理的なハードニングも忘れずに  

## 監視運用
エージェント型、機器メーカや通信会社が提供している機器とのセットサービスや監視サービス系はたくさん有る  
エージェントレス型や機種依存しないものは無いかな  

### ROEACOM Air
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://soracom.jp/services/air/" data-iframely-url="//cdn.iframe.ly/1lQmTY2?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

> SORACOM Air for セルラーは、IoT 向けのデータ通信 SIM "IoT SIM" を提供するサービスです。  
> 1枚から購入可能​で、最安基本料金50円〜/月と低コストで始められます。Web/APIで通信の運用監視、容易な​クラウド連携、閉域網接続や​オンデマンドリモートアクセスにも対応しており、IoTのつなぐを簡単にします。  

IoT に特化した SIMカードを提供している  

<i class="fas fa-chevron-circle-right"></i> 通信をきめ細かにコントロール可能な API  

> SORACOM Air for セルラーを利用することで API や Web ブラウザによるユーザーコンソールからデータ通信速度の変更、使用開始/休止といった IoT SIM の状態、データ使用量の監視、IoT SIM の ICCID(IC Card IDentifier)、IMSI(International Mobile Subscriber Identity)や通信モジュールの IMEI(International Mobile Equipment Identity)の取得など IoT デバイスのデータ通信を管理することができます。  

<i class="fas fa-chevron-circle-right"></i> イベントハンドラー  

> 特定のSIM・特定のグループ・特定のタグ・全SIMに対してデータ転送容量の閾値を元に、メールでの通知・速度クラスの変更・AWS Lambdaファンクションの実行をする事ができる機能です。  
<i class="fas fa-chevron-circle-right"></i> IMEI ロック機能  

> 指定したIMEI(端末識別番号:International Mobile Equipment Identifier)でのみ IoT SIM の通信を許可することができます。

<i class="fas fa-chevron-circle-right"></i> CHAP 認証機能  

> CHAP 認証機能を利用することで、お客様は任意にユーザー名、パスワードを設定できます。端末側に設定されたユーザー名、パスワードと SORACOM Air の SIMグループでお客様が設定した値とがマッチした場合に限りデータセッションの確立ができるようになります。  

<i class="fas fa-chevron-circle-right"></i> カスタム DNS の設定  

> IoT SIM を使用するデバイスに独自の DNS サーバーを指定することができます。  

<i class="fas fa-chevron-circle-right"></i> API を使用したデバイスへの SMS 送信  

> API を使用して SMS を送ることができます。これにより、携帯電話通信網外のサーバーからも IoT SIM を使用している IoT 機器に対して SMS を送信したり、プログラムに組み込んで送信を自動化することが可能になります。  

<i class="fas fa-chevron-circle-right"></i>  USSD 機能  

> SSD とは、Unstructured Supplementary Service Data の略です。スマートフォンでは、電話アプリなどで特殊な番号にダイヤルすることで USSD を使用してデータを送信することができます。また、デバイスから AT コマンドを用いて送信することもできます。  

### KDDI IoTクラウド デバイス管理
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://iot.kddi.com/services/iot-cloud-device/" data-iframely-url="//cdn.iframe.ly/lOprUfv?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

> 「KDDI IoTクラウド デバイス管理」は、点在する複数のお客さまIoT機器または通信モジュールのファームウェア更新や状態監視、位置情報・電波強度・電池残量の確認などをウェブ上のサービスポータルで遠隔管理できるサービスです。  

<i class="fas fa-chevron-circle-right"></i> リモート環境からの回線状態管理  
<i class="fas fa-chevron-circle-right"></i> リモート環境からのお客さまIoT機器状態管理  
<i class="fas fa-chevron-circle-right"></i> デバイス遠隔設定 (オプション)  
<i class="fas fa-chevron-circle-right"></i> ファームウェア更新 (FOTA) (オプション)  

対象端末として下記通信モジュールを装備した機器である必要がある  

* <i class="fas fa-external-link-alt"></i> [KYW01](https://iot.kddi.com/services/lpwa-modules/)  
* LwM2Mクライアントを搭載した通信モジュール  

結局エージェント型だった  

### TrendMicro オンラインスキャン for Home Network  
<i class="fas fa-external-link-alt"></i> [オンラインスキャン for Home Network](https://www.trendmicro.com/ja_jp/forHome/products/hw_onlinescan.html?AAID=iotsechl)  

> パソコン、スマートフォン、ルータ、Webカメラ、ゲーム機など、今やたくさんのデバイス（機器）が家庭のネットワークにつながっています。それらに対し、適切な設定・利用をしないと、知らない誰かが ご家庭のネットワークを使ったり、通信を盗み見られてしまったり、ウイルス感染させられてしまったりするかもしれません。  
> セキュリティはパソコンやスマートフォンだけでなく、ご家庭内のあらゆるインターネット接続機器に必要です。2016年8月に確認されたウイルス「Mirai」は、設定に不備のあったWebカメラに感染するものでした。  
> オンラインスキャン for Home Networkは、家庭内のネットワークにセキュリティ上のリスクが存在しないかをチェックできる無料ツールです。  

無償・有償とある個人向けツール  
エージェントレスでは無いけれど、集約管理PCとか有るんなら入れても良いかもだし、初期のセットアップ時に診断してもいいかも  

### パトロールクラリス  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://patrolclarice.jp/" data-iframely-url="//cdn.iframe.ly/mc0eXFe"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

> エージェントレス型サーバ監視・ネットワーク統合監視  

IoT機器に対してどこまで出来るかは微妙そう  

### IoTセキュリティ診断サービス
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.lac.co.jp/service/consulting/iot.html" data-iframely-url="//cdn.iframe.ly/wRV1poi?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

インターネと経由でのセキュリティ診断サービス  

---

まだまだ IoT 機器については運用や監視、セキュリティ診断といったサービスはあるけど拡充してはいないという印象  
ガイドラインを読んでそうか監視運用が大事なのねと思ってもそれに対する回答サービスが無い  
大規模に工場とかに入れるなら大手と組んでとかあるかもしれないけどこれから色々問題が置きて、乗り越えてという時期なのかな  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4844368249/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/51EIov0ufuL._SL160_.jpg" alt="未来IT図解 これからのIoTビジネス" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
