+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工作７"
tags = ["iot"]
categories = ["SE"]
date = "2021-02-25"
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

## アナログジョイスティック  

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x Joystick module 
* (5) x F-M wires (Female to Male DuPont wires) 

**ジョイスティックモジュール**  

{{< rawhtml >}} 
<div style="text-align: center;;">
<a href="https://www.amazon.co.jp/HiLetgo%C2%AE-%E3%82%B8%E3%83%A7%E3%82%A4%E3%82%B9%E3%83%86%E3%82%A3%E3%83%83%E3%82%AF-%E3%83%96%E3%83%AC%E3%83%BC%E3%82%AF%E3%82%A2%E3%82%A6%E3%83%88-%E3%82%B7%E3%83%BC%E3%83%AB%E3%83%89%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB-arduino%E3%81%AB%E5%AF%BE%E5%BF%9C/dp/B00M0F21KU?&linkCode=li2&tag=sinokyoufu-22&linkId=67462830aea0f40a51c3f16b3c95cf85&language=ja_JP&ref_=as_li_ss_il" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B00M0F21KU&Format=_SL160_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li2&o=9&a=B00M0F21KU" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}

> モジュールには VCC、グランド、X、Y、キーの 5 つのピンがあります。  
モジュールを入手した場所によって、あなたのラベルが若干異なる場合があります。  
親指の棒はアナログであり、単純な「指向的な」ジョイスティックよりも正確な読み取りを提供する必要があります。  
さらに、ジョイスティックを押し下げてプッシュボタンをアクティブにすることができます。  
X / Y ピンからデータを読み出すにはアナログの Arduino ピンを使用し、ボタンを読むにはデジタルピンを使用する必要があります。  
Key ピンは、ジョイスティックが押されるとグランドに接続され、そうでなければフローティングになります。  
キー/セレクト・ピンから安定した読み出しを得るには、プルアップ抵抗を介して VCC に接続する必要があります。  

ジョイスティックのキャップ以外、基盤部分はどのような仕組みでスティックの動きを数字化しているのか？  
ピン名からわかるように、X,Y 軸方向お読み取りと、SW スイッチによる ON/OFF を読み取っている  

ON/OFF など今までデジタル入力についてずっと色んなモジュールで触ってきたけれどアナログ入力とは？  

X,Y 軸の動きは電圧の変化として入力されアナログ値として読み取られる  
Arduino のアナログ入力は 10ビット分解能なので 0V～5Vの電圧を 0 ~ 1023 の値で表現される  

真上からみてコントローラーの左右上下がそれぞれ 0 ~ 1023  
中心にあるデフォルトの場合 x,y それぞれ 511 くらいってことになる  
基盤部分は スイッチの ON/OFF と 電圧の変化を伝えてるってことですね  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.arduino.cc/reference/en/language/functions/analog-io/analogread/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://www.arduino.cc/reference/en/language/functions/analog-io/analogread/" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">analogRead() - Arduino Reference</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">The Arduino programming language Reference, organized into Functions, Variable and Constant, and Structure keywords.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

**構成**  

<img src="/images/2021/se/arduino/tutorial-41.png" />  

先に述べたように X,Y はアナログ入力なため アナログピンに  
以前学習した [スイッチ](/2021/02/10/se-arduino3/) 同様に SW は デジタルピンに  
GND、5Vはそれぞれピンに  

いつもどおりサンプルプログラムをデプロイし  
シリアルモニタを開くと X,Y の座標とスイッチの状態が取得できている  

<img src="/images/2021/se/arduino/tutorial-42.png" />  

スティックを動かすと値の変化を確認できるだろう  
微妙にデフォルト値が 511 ではなくずれているけどそこはキットの精度なんでご愛嬌かな  

コードを見てみると  

```
// Arduino pin numbers
const int SW_pin = 2; // digital pin connected to switch output
const int X_pin = A0; // analog pin connected to X output
const int Y_pin = A1; // analog pin connected to Y output

void setup() {
  pinMode(SW_pin, INPUT);
  digitalWrite(SW_pin, HIGH);
  Serial.begin(9600);
}

void loop() {
  Serial.print("Switch:  ");
  Serial.print(digitalRead(SW_pin));
  Serial.print("\n");
  Serial.print("X-axis: ");
  Serial.print(analogRead(X_pin));
  Serial.print("\n");
  Serial.print("Y-axis: ");
  Serial.println(analogRead(Y_pin));
  Serial.print("\n\n");
  delay(500);
}
```

各ピンを指定し  
`digitalRead()`、`analogRead()`でそれぞれ読み取り表示しているだけのシンプルなコード  
スイッチ時同様に、ブレボで 入力値に基づいて RGB RED の色を変化させるとかやったら面白そう  

またよく電池残量を知る仕組みとかあるけど、電圧の変化によるアナログ出力からとってるのかなー？  

## 赤外線受信モジュール  

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x IR receiver module 
* (1) x IR remote 
* (3) x F-M wires (Female to Male DuPont wires) 

**赤外線受信モジュール IR RECEIVER SENSOR**  

{{< rawhtml >}} 
<div style="text-align: center;;">
<a href="https://www.amazon.co.jp/-/en/GAOHOU-KY-022-37-9KHz-Receiver-Arduino/dp/B074KCNHY3?&linkCode=li2&tag=sinokyoufu-22&linkId=f8c86af5924a8d53e1c093d8106a8dba&language=en_US&ref_=as_li_ss_il" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B074KCNHY3&Format=_SL160_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=en_US" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=en_US&l=li2&o=9&a=B074KCNHY3" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}

> IR 検出器は、赤外線を受信するように調整されたフォトセルを備えたマイクロチップです。 
ほとんどの場合、リモコンの検出に使用されます。  
テレビや DVD プレーヤーの前面には、クリッカーからの IR 信号を受信するためのものがあります。  
リモコンの内部には一致する IR LED があり、赤外線パルスを発してテレビの電源をオン、オフ、または変更するようにテレビに指示します。  
赤外光は人間の目には見えません。  
IR 検出器はデジタル出力です。これらは 38KHz  IR 信号を検出してロー（0V）を出力するか、いずれも検出せず、ハイ（5V）を出力します。  
光電池は抵抗器のように振る舞い、どれだけの光が当たるかによって抵抗が変化します。  

赤外領域の光（赤外線）を受光し電気信号に変換して、必要な情報を取り出して応用する技術、またその技術を利用した機器。人間の視覚を刺激しないで物を見られる、対象物の温度を遠くから非接触で瞬時に測定できるなどの特徴を持つ。  
と Wikipedia にも書いてあるとおり割と身近なもの  

そして電磁波の一種であるため、通信は波によって伝わります  
詳しくは下記を参考にして方が良いと思うけど  

赤外線を利用したリモコン通信はモールス信号のように ON/OFF を組み合わせてやりとりしている（PPM パルス位相変調）  
PPM方式にはフォーマットがあり、「NECフォーマット」、「Sonyフォーマット」等がある  

※PPMは以前出てきたPWMとは別  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="http://www.256byte.com/remocon.htm#:~:text=%E3%83%AA%E3%83%A2%E3%82%B3%E3%83%B3%E3%81%A8%E6%9C%AC%E4%BD%93%E3%81%AE%E9%80%9A%E4%BF%A1&amp;text=%E3%83%A2%E3%83%BC%E3%83%AB%E3%82%B9%E4%BF%A1%E5%8F%B7%E3%81%A7%E3%81%AF%E6%96%87%E5%AD%97%E3%82%92,%E6%9C%AC%E4%BD%93%E3%81%B8%E4%BC%9D%E3%81%88%E3%82%89%E3%82%8C%E3%81%BE%E3%81%99%E3%80%82" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?http://www.256byte.com/remocon.htm#:~:text=%E3%83%AA%E3%83%A2%E3%82%B3%E3%83%B3%E3%81%A8%E6%9C%AC%E4%BD%93%E3%81%AE%E9%80%9A%E4%BF%A1&amp;text=%E3%83%A2%E3%83%BC%E3%83%AB%E3%82%B9%E4%BF%A1%E5%8F%B7%E3%81%A7%E3%81%AF%E6%96%87%E5%AD%97%E3%82%92,%E6%9C%AC%E4%BD%93%E3%81%B8%E4%BC%9D%E3%81%88%E3%82%89%E3%82%8C%E3%81%BE%E3%81%99%E3%80%82" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">FUTABA HOME</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;"></span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

PWMと似たような新しく PPM という単語が出てきましたね  
他にもいくつかあるようですが要はパルス、ON/OFF による短波系をどうデータに変調させるかの方式たちのこと(かな？)  
それぞれにメリット・デメリットがあるらしい、わからん  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://ja.wikipedia.org/wiki/%E3%83%91%E3%83%AB%E3%82%B9%E5%A4%89%E8%AA%BF" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">パルス変調 - Wikipedia</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;"></span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

今回のチュートリアルで利用するのはレシーバーモジュール  
リモコン等から赤外線通信した際にどのようなデータが受信されているのか確認してみようって感じかな  
赤外線通信自体は昔からある成熟された技術なのでか、初学者に対してわかりやすく纏まってるきれいなサイトって中々無いね・・・  

**構成**  

<img src="/images/2021/se/arduino/tutorial-43.png" />  

学習キットに含まれてるのは AX-1838HS という IR赤外線レシーバーモジュール  
レシーバー自体には 3本ピンがありそれぞれ Vcc,GND,DATAとなっていて  
モジュールになってもそこは変わらず３本のピンとなっていてそれぞれ  

G：GND  
R：Vcc  
Y：DATA  

となっている（なんでモジュール化するとピン配置かえるんじゃ？）  

構成通りに組み、いつもどおりサンプルプログラムとライブラリをデプロイして  
付属のリモコンでセンサーに向かってボタンを押すと下記のようにシリアル出力される  

<img src="/images/2021/se/arduino/tutorial-44.png" />  

一番下の`other button`は他家にあったリモコンから送った場合のデータ  

コードを見てみよう  

```
#include "IRremote.h"

int receiver = 11; // Signal Pin of IR receiver to Arduino Digital Pin 11

/*-----( Declare objects )-----*/
IRrecv irrecv(receiver);     // create instance of 'irrecv'
decode_results results;      // create instance of 'decode_results'

/*-----( Function )-----*/
void translateIR() // takes action based on IR code received

// describing Remote IR codes 

{

  switch(results.value)

  {
  case 0xFFA25D: Serial.println("POWER"); break;
  case 0xFFE21D: Serial.println("FUNC/STOP"); break;
  case 0xFF629D: Serial.println("VOL+"); break;
  case 0xFF22DD: Serial.println("FAST BACK");    break;
  case 0xFF02FD: Serial.println("PAUSE");    break;
  case 0xFFC23D: Serial.println("FAST FORWARD");   break;
  case 0xFFE01F: Serial.println("DOWN");    break;
  case 0xFFA857: Serial.println("VOL-");    break;
  case 0xFF906F: Serial.println("UP");    break;
  case 0xFF9867: Serial.println("EQ");    break;
  case 0xFFB04F: Serial.println("ST/REPT");    break;
  case 0xFF6897: Serial.println("0");    break;
  case 0xFF30CF: Serial.println("1");    break;
  case 0xFF18E7: Serial.println("2");    break;
  case 0xFF7A85: Serial.println("3");    break;
  case 0xFF10EF: Serial.println("4");    break;
  case 0xFF38C7: Serial.println("5");    break;
  case 0xFF5AA5: Serial.println("6");    break;
  case 0xFF42BD: Serial.println("7");    break;
  case 0xFF4AB5: Serial.println("8");    break;
  case 0xFF52AD: Serial.println("9");    break;
  case 0xFFFFFFFF: Serial.println(" REPEAT");break;  

  default: 
    Serial.println(" other button   ");

  }// End Case

  delay(500); // Do not get immediate repeat

} //END translateIR
void setup()   /*----( SETUP: RUNS ONCE )----*/
{
  Serial.begin(9600);
  Serial.println("IR Receiver Button Decode"); 
  irrecv.enableIRIn(); // Start the receiver

}/*--(end setup )---*/


void loop()   /*----( LOOP: RUNS CONSTANTLY )----*/
{
  if (irrecv.decode(&results)) // have we received an IR signal?

  {
    translateIR(); 
    irrecv.resume(); // receive the next value
  }  
}/* --(end main loop )-- */
```

ものすごいベタにセンサー入力値を `switch` 文でぶん回してマッピングさせてる  
実際の値を知りたい場合は `Serial.print(results.value);` を `translateIR()` 関数の中に書いてあげれば表示してくれる 

例えば「Vol-」ボタンを押すと  

```
16:45:08.546 -> 16754775 VOL-
```

となる  
しかし switch 文の case に書かれてる文字と違うのがわかる  
例えばパワーボタンの `0xFFA25D`、 `0x` は 16進数であることの宣言なので  
その後に続く `FFA25D` が実際に取得しているデータの１６進数番という事  

試しに `Serial.print(results.value, HEX);` にすることで 16進数にした値を確認できる  

```
17:00:03.666 -> FFE21D FUNC/STOP
17:32:31.701 -> A90 other button 
```

レシーバー自体はデジタル入力なので２進数なのを、赤外線データのようなリモコン等多数のボタンが付くもので網羅しようとすると多種、及び長大になるため  
コード上は１６進数で制御している感じだね  

Arduino 周りで赤外線レシーバーで遊んでる人見ると  
これでパスワード認証を構築している人もいて面白かったりする  

---

ついに登場アナログ入力と  
デジタル入力における制御と通信の仕組みなどちょっとづつ応用的になってきた感じかな？  

リモコン側のコーディングができるようになればまさに、スマートリモコンが作れるんだろうね  
このチュートリアルで登場した物単体は外部入力に基づく数値化と表示だけなんだけど  
身の回りのものとの関連や制御対象に対するコントローラーと見た場合色々面白そうな仕組みです  

とりあえず今回はここまで  
多分次回に続く  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
