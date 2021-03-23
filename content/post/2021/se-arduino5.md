+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工作５"
tags = ["iot"]
categories = ["SE"]
date = "2021-02-17"
description = "ELEGOO Arduino用UNO R3スターターキット チュートリアル入門"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

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

## 傾斜ボールスイッチ

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x Tilt Ball switch 
* (2) x F-M wires (Female to Male DuPont wires) 

**傾斜センサー**  

<img src="/images/2021/se/arduino/tutorial-29.png" />  

> 傾斜センサ（傾斜ボールスイッチ）を使用すると、向きや傾きを検出できます。それらは小さく、安
価で、低電力で使いやすいものです。正しく使用すると、磨耗しません。彼らのシンプルさは、おも
ちゃ、ガジェット、家電製品で人気があります。明らかな理由から、「水銀スイッチ」、「傾斜スイ
ッチ」または「ローリングボールセンサー」と呼ばれることもあります。  

中に入ってるボールが分側の基部に触れていると通電し、それ以外はOFFになる  
よくフィクションで見る爆弾のスイッチに使われてて、銀の玉が触れると爆発！ってある水銀スイッチはこれのことかー！  
傾斜センサーとしても使えるがスイッチと見たほうが理解しやすい  

**構成**  
スイッチのピンに正負はない  

<img src="/images/2021/se/arduino/tutorial-30.png" />  

いつものようにサンプルコードをデプロイし  

<img src="/images/2021/se/arduino/tutorial-31.png" />  

シンプルにスイッチがONの場合は Arduino 本体の LED が光り、OFFの場合は消えることを確認できる  
真上、真下にいなくても、筒の太さ、長さ、ボールの大きさからわかるようにちょっとの傾斜でON/OFFになる事がわかる  

```
void loop() 
{  
  int digitalVal = digitalRead(2);
  if(HIGH == digitalVal)
  {
    digitalWrite(ledPin,LOW);//turn the led off
  }
  else
  {
    digitalWrite(ledPin,HIGH);//turn the led on 
  }
}
```

デジタルの２番ピンを`digitalWrite(2, HIGH)`に設定し  
コードを見てみると、スイッチによるデジタル入力値`digitalRead(2)`により  
`digitalVal` が HIGH なら `ledPin,LOW`、違うなら `ledPin,HIGH` にしている  

## サーボ

使用する機器  

* (1) x Elegoo Uno R3  
* (1) x Servo (SG90)  
* (3) x M-M wires (Male to Male jumper wires)  

**サーボ（SG90）**  

<img src="/images/2021/se/arduino/tutorial-32.png" />  

* Universal for JR and FP connector 
* Cable length : 25cm 
* No load; Operating speed: 0.12 sec / 60 degree (4.8V), 0.10 sec / 60 degree (6.0V) 
* Stall torque (4.8V): 1.6kg/cm 
* Temperature : -30~60'C 
* Dead band width: 5us 
* Working voltage: 3.5~6V 
* Dimension : 1.26in x 1.18 in 0.47 in (3.2cm x 3cm x 1.2cm) 
* Weight : 4.73 oz (134g) 

> サーボは 180 度回転することができるギヤードモータの一種です。  
UNO R3 ボードから電気パルスを送信することによって制御されます。  
これらのパルスは、サーボがどの位置に移動すべきかをサーボに指示する。  
Servo には 3 本のワイヤーがあり、茶色のワイヤーはグランドワイヤーで、UNO のGND ポートに接続する必要があり、赤色のワイヤーは電源ワイヤーで、5v ポートに接続し、オレン
ジ色のワイヤーは信号ワイヤー  Dig＃9 ポートに接続する必要があります。  

サーボモーターといえば名前は聞いたことあるが普通のモーターと何が違うのか  

* モーター：電力をかけるとひたすら回転  
* サーボモーター：信号に応じて0度からN度の範囲で回転  
* ステッピングモーター：信号に応じてモーターを制限なく制御可能  
* ブラシレスモーター：安定した回転速度などの制御が可能  

サーボモーターは PWM パルス幅変調 と RGB LED 時に利用した仕組みを利用して制御する  

詳しい動作については下記を参考にするとわかりやすい  
<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://burariweb.info/electronic-work/arduino-learning/aruduino-servomotor-control.html" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:i0.wp.com/burariweb.info/wp-content/uploads/2020/10/aruduino-servomotor-control-46.jpg?fit=720%2C480&amp;#038;ssl=1" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">【Arduino入門編⑧】サーボモーターを動かしてみる。PWM制御についていの解説です！</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">【Arduino入門編⑧】サーボモーターを動かしてみる。PWM制御についていの解説です！</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

SG90 というサーボモーターの場合  
周期20ms でデューティーサイクル 0.5~2.4ms なので  
周期20msのパルスを0.5ms HIGH にすると0度、2.4ms HIFGH で 180 度動くということらしい  

<img src="/images/2021/se/arduino/tutorial-34.png" />  

こういうデータシートの見方はちゃんと読めるようにしておきたい  
チュートリアルにはそこまで詳しくは説明がないため、「この機器はなんなのか？」「どういう仕組だ？」  
という疑問と自分で調べることが求められる  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://akizukidenshi.com/catalog/g/gM-08761/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://akizukidenshi.com/catalog/g/gM-08761/" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">マイクロサーボ９ｇ　ＳＧ−９０: パーツ一般 秋月電子通商-電子部品・ネット通販</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">電子部品,通販,販売,半導体,IC,マイコン,電子工作マイクロサーボ９ｇ　ＳＧ−９０秋月電子通商 電子部品通信販売</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

**構成**  

<img src="/images/2021/se/arduino/tutorial-33.png" />  

サーボモーターの PWM 対応のオレンジ を ARduinoの PWM対応 9ピンに  
GROUND（ー） 対応の 茶色は GND ピンに  
Vcc（＋）対応の赤は 電源の 5V ピンに  

サンプルコードを開き、ブザー時と同様にライブラリを読み込んでいるため  
ライブラリを追加してデプロイするとモーターが動き出す  
※私の環境では Servo ライブラリはデフォルトでインストールされていた  

コードを見てみると  

```
#include <Servo.h>

Servo myservo;  // create servo object to control a servo
// twelve servo objects can be created on most boards

int pos = 0;    // variable to store the servo position

void setup() {
  Serial.begin(9600);
  myservo.attach(9);  // attaches the servo on pin 9 to the servo object
}

void loop() {
  for (pos = 0; pos <= 180; pos += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo.write(pos);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  for (pos = 180; pos >= 0; pos -= 1) { // goes from 180 degrees to 0 degrees
    myservo.write(pos);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
 
}
```

サーボ制御につかう PWM ピンを `myservo.attach(9)` で指定し  
`int pos = 0;` で現在のモーター位置を原点設定  
その後 `for` 文で `pos` 値を `0~180` つまり 0度～180度 まで 1度 ずつ増減させながらループさせている  
RGB LED 時と違い、PWM の間隔を調整するような記述は無く、Servo ライブラリの関数 `myservo` を利用することで簡単に制御できるようになっている  

こころなしかきっちり 180度 で動いてる気はしないが、そこはモーターの精度の話だろう  

---

よくおもちゃとかであるような簡単な機器が出てきた  
へーといいながら作業してたり、データシートをちゃんと調べ始める必要が出てきてる  

とりあえず今回はここまで  
多分次回に続く  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
