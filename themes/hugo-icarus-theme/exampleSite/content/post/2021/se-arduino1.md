+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工作１"
tags = ["iot"]
categories = ["SE"]
date = "2021-02-08"
description = "ELEGOO Arduino用UNO R3スターターキット チュートリアル入門"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

## ELEGOO Arduino用UNO R3スターターキット レベルアップ チュートリアル付 mega2560 r3 nanoと互換

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B06XF2HZGT&linkId=0f6f39010078d52b6dd7106fceb44609"></iframe>
</div>
{{< /rawhtml >}}

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.elegoo.com/pages/arduino-kits-support-files" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:cdn.shopify.com/s/files/1/0296/9026/5648/files/308ec23709872bfbfd2a2b975b121008_ab56db2e-c179-43c0-b357-9dae066a2b93.jpg?v=1611368475" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Arduino Kits User Support</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ELEGOO specializes in the research, development, and production of the most quality &amp; affordable 3D printers, Arduino-based STEM &amp; robotic kits, making technolegy easy-to-access for all the makers around the world.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

ラズパイは持ってるんだけど様々な IoT 機器や センサー類の使用をしていきたいなとなると  
基礎的な知識も抑えておきたいと思って年末の割引セール時に買ってみた  

とりあえず電子工作に関しては素人なので基本的には購入した Elegoo のチュートリアルに沿って触っていく  
付属のCDには日本語のチュートリアルもあるため、以降はその流れで学習する  
付属のCD Versionは "Elegoo The Super Starter Kit for UNO V1.0.2019.09.17" でelegooのサイトにあるものから古くなってるが、日本語チュートリアルがあるのは助かる  

学習の備忘録的な面が強いです  

## 1. 製品構成
第一章は内容物の確認と Arduino UNO R3 の基本的な説明  

### PackingList
> このチュートリアルでは、初心者向けに の使用方法についての基本的な情報をすべて学ぶことができます。  
Arduinoのコントローラボード、センサー、コンポーネント。Arduinoをもっと深く勉強したいなら  
Michael Margolis氏が書いたArduino Cookbookを読むことをお勧めします。  
このチュートリアルのいくつかのコードはSimon Monkによって編集されています。Simon Monkは多くの本の著者です。  
オープンソースハードウェアに関連するものです。それらはAmazonで購入できます。プログラミングArduino, 30 Arduino  
悪の天才とRaspberry Piのプログラミングのためのプロジェクト。  

※機械翻訳注意  

### First Look Arduino
Arduino UNO R3 の概要と各基部の説明  

<img src="/images/2021/se/arduino/tutorial-01.png" />  

**GND**：  
「GND」は「グランド」と読みます。0Vの基準点を示してくれるピンで、電池のマイナス極と似た役割を持っています。デジタルピンから電圧をかけるときは、最終的にこのピンに結線して、回路を作ります。GNDにつなぐことで、電圧が5Vになることを担保します。  

**5V/3V**：  
5Vピンは5ボルトの電力を供給し、3.3Vピンは3.3ボルトの電力を供給します。  
ボルトを供給します。Arduinoで使われている簡単な部品のほとんどは、5ボルトでも3.3ボルトでも問題なく動作します。  

**Analog**：  
アナログインラベルの下にあるピンの領域（UNO の A0～A5）がアナログインピンです。れらのピンは、アナログセンサー（温度センサーのようなもの）からの信号を読み取って、読み取れるデジタル値に変換することができます。  

**Digital**：  
デジタルピン（UNO の 0～13）があります。これらのピンはデジタル入力（ボタンが押されているかどうかを知らせるようなもの）とデジタル出力（LEDへの電力供給のようなもの）の両方に使用することができる。  
デジタルという名前の通り、HIGHとLOWだけを扱えます。電圧をかけるときは5V、かけないときは0Vになります。電圧を読み取るときも、HIGHとLOWの区別だけができます。  
穴のすぐ下に、0から13の数字が書いてありますが、これがピンのIDを表しています。プログラムを書くときは、「DIGITAL PINの13番から電圧をかける」というような処理を記述します。  

**PWM**：  
デジタルピンの隣にチルダ(~)があることにお気づきかもしれません。  
(UNO では 3, 5, 6, 9, 10, 11)。これらのピンは通常のデジタルピンとして機能しますが、以下のような用途にも使用できます。  
パルス幅変調（PWM）と呼ばれるもの。PWM のチュートリアルがありますが、今のところは  
これらのピンは、アナログ出力（LEDのフェードイン、フェードアウトなど）をシミュレートすることができると考えられています。  

**AREF**：  
Analog Reference の略です。ほとんどの場合、このピンは放っておいても大丈夫です。アナログ入力ピンの上限として外部基準電圧（0～5 ボルト）を設定するために使用されることがあります。  

## 2. Arduino と Arduino IDE

### Arduino IDE のインストール  
Arduino IDE のインストール、準備と基本的な操作の学習  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.arduino.cc/en/software" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:content.arduino.cc/assets/arduino_logo_1200x630-01.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Software</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Open-source electronic prototyping platform enabling users to create interactive electronic objects.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>  

Windows向けには インストーラーと WindowsAppStore版がある、Web版もあるみたい  
インストール完了後、起動するとこんなかんじ  

<img src="/images/2021/se/arduino/tutorial-02.png" />  

次に Arduino と PCを付属の USB でつなぎ、ドライバをインストールする  
チュートリアルに Windows のデバイス自動認識によるインストールは信用するなと書かれてて笑  

現時点の Arduino においては特に問題なくドライバはインストールされるみたいで  
確認してみると下記のように認識されている  

<img src="/images/2021/se/arduino/tutorial-03.png" />  

識別されない問題は昔 Arduino のドライバが署名されてない時代の名残なのかな  

チュートリアルに従い IDE にライブリの追加  
メニューの「スケッチ」「ライブラリをインクルード」「ライブラリを管理」  

<img src="/images/2021/se/arduino/tutorial-04.png" />  

チュートリアルでは blink をインストールとなているが最初からインストール済みなので  
インストールされていること等を確認しておく  

その他にも Zip アーカイブから　サードパーティのライブラリを追加することもできるようなので覚えておく  

### 接続確認
IDE にて 作業PCに USB 接続している Arduino がちゃんと認識されていることを確認し  
※認識されているポートは作業環境によるので各個で確認  

<img src="/images/2021/se/arduino/tutorial-05.png" />  

問題なければ右上の接続ボタンを押すと下記のようにウィンドウが開いてシリアル接続できる  

<img src="/images/2021/se/arduino/tutorial-06.png" />  

### 点滅
> UNO R3 ボードには、複数の電子デバイスとその機能を拡張するプラグインのシールドに接続するために使用される、両側に沿ったコネクタ列があります。  
また、スケッチから制御できる単一の LED もあります。  
この LED は UNO  R3 ボードに内蔵されており、ボード上にラベル付けされているので、しばしば「L」LED と呼ばれます。  
UNO R3 ボードの「L」LED は、USB プラグに接続すると既に点滅しています。  
これは、ボードには一般に「Blink」スケッチがプリインストールされて出荷されるためです。
このレッスンでは、UNO R3 ボードを独自の Blink スケッチで再プログラムし、点滅するレートを変更します。  

<img src="/images/2021/se/arduino/tutorial-07.png" />  

Arduino IDE と接続した Arduino にプログラムをデプロイして RED の点滅を操作するチュートリアル  
IDEから「ファイル」「スケッチ例」「01.Basics」「Blink」  

<img src="/images/2021/se/arduino/tutorial-08.png" />  

先程のチュートリアルでインストールを確認した Blink プログラムが Arduino の電源が入っているときにボードの LED 点滅を行っているプログラム  
中身を見てみると  

```
// the setup function runs once when you press reset or power the board
void setup() {
  // initialize digital pin LED_BUILTIN as an output.
  pinMode(LED_BUILTIN, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(LED_BUILTIN, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);                       // wait for a second
  digitalWrite(LED_BUILTIN, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);                       // wait for a second
}
```

チュートリアルと違い、LEDピン名の宣言が無い  

> すべての Arduino スケッチには 'setup'機能が必要です。
この場合、そこにコマンドが 1 つしかありません。コメント状態が Arduino ボードに LED ピンを出力として使用するように指示しています。  
スケッチが  'ループ'機能を持つことも必須です。  
一度しか動作しない「セットアップ」機能とは異なり、リセット後、「ループ」機能はコマンドの実行が終了した後すぐに再び開始されます。  

パラメメータを変更し保存、「マイコンボードに書き込む」を実施するとデプロイされ  
LEDの点滅間隔が変わっていることを確認できる  

<img src="/images/2021/se/arduino/tutorial-09.png" />  

---

とりあえず今回はここまで  
次回に続く（多分）  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
