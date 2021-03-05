+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工８"
tags = ["iot"]
categories = ["SE"]
date = "2021-03-05"
description = "ELEGOO Arduino用UNO R3スターターキット チュートリアル入門"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

## ELEGOO Arduino用UNO R3スターターキット レベルアップ チュートリアル付

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B06XF2HZGT&linkId=0f6f39010078d52b6dd7106fceb44609"></iframe>
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B008GRTSV6&linkId=ee1a6cf278bcb44b6852920c69f345b8"></iframe>
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B06Y56JV64&linkId=8ac38b77484563dbef2fbc76ab41e889"></iframe>
</div>
{{< /rawhtml >}}

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.elegoo.com/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:cdn.shopify.com/s/files/1/0296/9026/5648/files/308ec23709872bfbfd2a2b975b121008_ab56db2e-c179-43c0-b357-9dae066a2b93.jpg?v=1611368475" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">ELEGOO: Best Consumer Level 3D Printers, STEM Kits &amp;amp; Robots</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ELEGOO specializes in the research, development, and production of the most quality &amp;amp; affordable 3D printers, Arduino-based STEM &amp;amp; robotic kits, making technolegy easy-to-access for all the makers around the world.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

前回の続き  
引き続きチュートリアルに沿って電子工作入門していく  

## LCD DISPLAY  

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x LCD1602 module 
* (1) x Potentiometer (10k) 
* (1) x 830 tie-points Breadboard 
* (16) x M-M wires (Male to Male jumper wires) 

**LCD DISPLAY**  

{{< rawhtml >}} 
<div style="text-align: center;;">
<a href="https://www.amazon.co.jp/SunFounder-LCD%E3%83%87%E3%82%A3%E3%82%B9%E3%83%97%E3%83%AC%E3%82%A4IIC-%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%95%E3%82%A7%E3%82%A4%E3%82%B9-LCD%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E3%82%B7%E3%83%BC%E3%83%AB%E3%83%89-Mega2560%E3%81%AB%E5%AF%BE%E5%BF%9C/dp/B081TYYLX4?pf_rd_r=75K3XTZW8XECCBKXZECR&pf_rd_p=20d94442-97c2-507d-9d49-b27d0be7d561&pf_rd_s=merchandised-search-10&pf_rd_t=BROWSE&pf_rd_i=3332611051&linkCode=li3&tag=sinokyoufu-22&linkId=6f6d25637508b37c2fb36ce9cf0e3b0b&language=ja_JP&ref_=as_li_ss_il" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B081TYYLX4&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li3&o=9&a=B081TYYLX4" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}

セット内の LCD DIsplay は「LCM1602A」という LCD にコントローラーをつけてモジュール化したもの  
<i class="fas fa-external-link-alt"></i> [データシート - datasheetspdf.com](https://datasheetspdf.com/pdf/866417/LONGTECHOPTICS/LCM1602A/1)  

LCD とは Liquid Crystal Display の略で液晶ディスプレイのこと  
液晶ディスプレイはバックライトが発する光を偏光フィルター、カラーフィルター、液晶を通過させることで色付きの光として出力されます  

液状ディスプレイは、制御するためのタイミングチャート、表示させるための文字コード、文字やその他表示制御用のインスラクション等を意識する必要があるが  
Arduino にはライブラリにより抽象化されているためほとんど意識しなくても扱うことができる  
<i class="fas fa-external-link-alt"></i> [LCDに文字を表示させるプログラミング - monoist.atmarkit.co.jp](https://monoist.atmarkit.co.jp/mn/articles/0703/19/news116.html)  
<i class="fas fa-external-link-alt"></i> [インストラクションによる液晶表示器の設定 - infoseek_rip.g.ribbon.to](http://infoseek_rip.g.ribbon.to/spectrum123.at.infoseek.co.jp/lcd/lcd_2/lcd_2.htm)  

このモジュールでは16ピンあり、それぞれ  

* VSS: グランドに接続するピン  
* VDD: + 5V 電源に接続するピン  
* VO: LCD1602 のコントラストを調整するピン  
* RS: LCD のメモリ内のどこにデータを書き込むかを制御するレジスタ選択ピン 画面に表示されている内容を保持するデータレジスタ、または命令のレジスタ（LCD のコントローラが次に何をするかの指示を参照する）を選択できます 
* R/W: 読み出しモードまたは書き込みモードを選択する読み出し/書き込みピン  
* E: ローレベルのエネルギーが供給されると、LDC モジュールに関連する命令を実行させるイネーブルピン 
* D0-D7: データを読み書きするためのピン  
* A and K: LED バックライトを制御するピン  

となっている  

**ポテションメータ**  

{{< rawhtml >}} 
<div style="text-align: center;;">
<a href="https://www.amazon.co.jp/10K%E3%82%AA%E3%83%BC%E3%83%A0%E3%83%9D%E3%83%86%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%A1%E3%83%BC%E3%82%BF%EF%BC%8CSODIAL-R-10%E4%BB%B6%E3%81%AE%E3%83%9D%E3%83%86%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%A1%E3%83%BC%E3%82%BF%E3%83%88%E3%83%AA%E3%83%9E%E5%8F%AF%E5%A4%89%E6%8A%B5%E6%8A%97-3362P-103-10K%E3%82%AA%E3%83%BC%E3%83%A0/dp/B01GNKVJX2?&linkCode=li3&tag=sinokyoufu-22&linkId=64bfe19c25c467095820301b40437f31&language=ja_JP&ref_=as_li_ss_il" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B01GNKVJX2&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li3&o=9&a=B01GNKVJX2" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}

上記のと見た目は違うがセットに含まれてるのは PT-15 というポテンションメータ（ノブ付き）  
[PT06PT15トリマーポテンショメータ (ノブ付き) - japanese.alibaba.com](https://japanese.alibaba.com/product-detail/pt06-pt15-trimmer-potentiometer-with-knob-60484680409.html)  

ポテンションメータとは抵抗値を任意の値に変更できる可変抵抗器のこと  
オーディオのボリューム調整といったアナログ電圧の制御・設定から、カメラのズームレンズ制御といった位置・角度検出センサまでと、幅広い用途で用いられている電子部品  

またボリューム等のように位置が変化することにより抵抗値、電圧が変わるため変位センサとしても利用される   
ワイパで機械的に位置を変化させ、その変位量と出力された電圧から距離・寸法などを測定する、というように  
カメラのズームレンズの制御や工作・建設機械などのアーム操作の角度検出などに用いられる  

LCDディスプレイの本チュートリアルではバックライトの調整に使う  

ポテンションメータのピンは3本あり、2本に電圧をかけて真ん中のピンから出力結果を得ます  
出力ピンはアナログ出力  
左ピンが VCC、、右ピンが GND になっている  

10k とは 10kΩのことで、最小0Ω、最大10kΩの調整が可能ということ  

**構成**  

<img src="/images/2021/se/arduino/tutorial-45.png" />  

ポテンションメータの出力先は LCD の VO ピンにつなげる  
RS、E、D4～D7 の制御用はすべてがデジタル入出力に繋げる  

いつもどおりサンプルコードをデプロイすると  
※必要により LiquidCrystal ライブラリを追加する。多くの場合デフォルトである  

<img src="/images/2021/se/arduino/tutorial-46.jpg" />  

'hello、world'というメッセージが表示され、その後にゼロからカウントアップする数字が表示されます。  
※メッセージが出ない場合、ポテンションメータによってコントラストが低すぎる場合があるので動かしてみると見える場合もある  

コードを見てみるとライブラリ様々でシンプル  

```
// include the library code:
#include <LiquidCrystal.h>

// initialize the library with the numbers of the interface pins
LiquidCrystal lcd(7, 8, 9, 10, 11, 12);

void setup() {
  // set up the LCD's number of columns and rows:
  lcd.begin(16, 2);
  // Print a message to the LCD.
  lcd.print("Hello, World!");
}

void loop() {
  // set the cursor to column 0, line 1
  // (note: line 1 is the second row, since counting begins with 0):
  lcd.setCursor(0, 1);
  // print the number of seconds since reset:
  lcd.print(millis() / 1000);
}
```

ピンの割当宣言と `LiquidCrystal lcd()`  

処理の setup は  
`lcd.begin()` で使用する LCD の行数と列数を指定し  
`lcd.print()` で表示させる文字を指定している  

２行目への文字は loop 内で指定している 
`lcd.setCursor()`が表示行の指定だね  

静的な文字の表示であれば setup と loop を分ける必要は無いが  
サンプルコードでは動的に変化する文字を表示しようとしている `lcd.print(millis() / 1000)` ため  
Loop 側に記述することで 処理されるたびに表示される値が変わるよう動的表示を制御している  

センサー等のデータから表示を動的に変えたい場合はこのやりかたが必要ってことかな  

本レッスンでサラッと登場したポテンションメータも、左右に弄ってみると液晶の表示具合の変化を確認できる  
ポテンションメータではなく普通の抵抗器でもできるし、無くても(つまり0Ω状態)大丈夫  

またなんで LCD の D0 から配線していないのか？という点については
下記リファレンスにもあるとおり D0 ~ D3 はオプションになっており  
[LiquidCrystal() - Arduino 日本語リファレンス](http://www.musashinodenpa.com/arduino/ref/index.php?f=1&pos=931)  

動作モードによって使うピンが変わるからである  
4つのデータピンを使用する4ビットモード（d4-d7）と8つのデータピンすべてを使用する8ビットモード（d0-d7）の2つの主要な動作モードがあり  
利用するディスプレイがどちらの動作モードをサポートしているかによって選択したり  
動作モードの違いにより Arduino の利用できるピン数も減ってしまうためそのへんの選択で変わってきます  

4bit モードと 8bit モードの違いは単純に表示速度ですが  
マイクロチップコントローラーの発展や色々と時代の経緯やなんやかんやがあるようです  
[マイクロコントローラ - Wiki](https://ja.wikipedia.org/wiki/%E3%83%9E%E3%82%A4%E3%82%AF%E3%83%AD%E3%82%B3%E3%83%B3%E3%83%88%E3%83%AD%E3%83%BC%E3%83%A9)  
[液晶表示モジュールを４ビットモードで使ったときの空きピン処理 - act-ele.c.ooco.jp](http://act-ele.c.ooco.jp/trouble/lcd4bit/lcd4bit.htm)  

ちょっとガチで勉強しないと何言ってるのか、何が問題なのかわからないのでふーんくらいで流しておきます  

今まではデジタル出力、アナログ出力共に Arduino IDE 等からシリアル出力結果を観測していたが  
今後はこの LCD を利用して表示すると良いかもしれない  

---

今まで単一の回路を動かしていたのが  
いろんな回路、機器を組み合わせて使うようになった  

今まで先にすすめるため流していた基礎知識部分も流せなくなってきたため  
調べ物しながらゆっくりと進めていこうと思う  
なによりディスプレイの登場で見た目的に動作がわかるのはやっぱりいいですね  

とりあえず今回はここまで  
多分次回に続く  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
