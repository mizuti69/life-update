+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工作３"
tags = ["iot"]
categories = ["SE"]
date = "2021-02-10"
description = "ELEGOO Arduino用UNO R3スターターキット チュートリアル入門"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

## ELEGOO Arduino用UNO R3スターターキット レベルアップ チュートリアル付

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B06XF2HZGT&linkId=0f6f39010078d52b6dd7106fceb44609"></iframe>
</div>
{{< /rawhtml >}}

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.elegoo.com/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:cdn.shopify.com/s/files/1/0296/9026/5648/files/308ec23709872bfbfd2a2b975b121008_ab56db2e-c179-43c0-b357-9dae066a2b93.jpg?v=1611368475" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">ELEGOO: Best Consumer Level 3D Printers, STEM Kits &amp;amp; Robots</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ELEGOO specializes in the research, development, and production of the most quality &amp;amp; affordable 3D printers, Arduino-based STEM &amp;amp; robotic kits, making technolegy easy-to-access for all the makers around the world.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

前回の続き  
引き続きチュートリアルに沿って電子工作入門していく  

## デジタル入力

### プッシュスイッチ
LED をオン/オフするためにデジタル入力付きのプッシュボタンを使用する方法
を学習します。   

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x 830 Tie-points Breadboard 
* (1) x 5mm red LED 
* (1) x 220 ohm resistor 
* (2) x push switches 
* (7) x M-M wires (Male to Male jumper wires) 

**push switches**  

<img src="/images/2021/se/arduino/tutorial-18.png" />  

ボタンを押すかレバーを回すと、2 つの接点が電気的に接続されます。  
これまた左右上下が間違えやすいので注意  

<img src="/images/2021/se/arduino/tutorial-19.png" />  
※LEDの正負ピンに注意

配線がもんだいなければ LED プログラムのままなので Arduino を繋いだ時点で LED が点灯する  

チュートリアル用のサンプルプログラムを開きデプロイする  
<img src="/images/2021/se/arduino/tutorial-20.png" />  

デプロイするとLEDが消え、左ボタンを押すと LED が点灯し、右ボタンを押すと消灯します。  

```
void setup() 
{
  pinMode(ledPin, OUTPUT);
  pinMode(buttonApin, INPUT_PULLUP);  
  pinMode(buttonBpin, INPUT_PULLUP);  
}

void loop() 
{
  if (digitalRead(buttonApin) == LOW)
  {
    digitalWrite(ledPin, HIGH);
  }
  if (digitalRead(buttonBpin) == LOW)
  {
    digitalWrite(ledPin, LOW);
  }
}
```
<br />

> スケッチの最初の部分は、使用される 3 つのピンの 3 つの変数を定義します。  
'ledPin'は出力ピンで、'buttonApin'はブレッドボードの上部に近いスイッチを指し、  'buttonBpin'は他のスイッチを指します。  
INPUT_PULLUP のピン・モードは、ピンを入力として使用することを意味しますが、他のものが入力に接続されていない場合は、「プルアップ」して HIGH にする必要があります。  
言い換えれば、入力のデフォルト値は、ボタンを押す動作によって LOW に引かれない限り、HIGH です。  
このため、スイッチは GND に接続されています。  
スイッチを押すと、入力ピンが GND に接続されるため、ハイになりません。    

> 入力が通常 HIGH で、ボタンを押したときに入力が LOW になるので、ロジックは少し上下逆です。  
これを'loop'関数で扱います。  

GND でマイナス電力は通しつつ、プログラム側でプラス電力のON/OFFを制御する仕組み  
デフォルトでデジタル入力は HIGH なため、左のボタンを押す＝LOWとなり  
loop内で LEDに電力を通すピンをHIGHにして電気を通す  
右のボタンが押されたら＝LOWとなりLEDピンをLOWにして電気を止める  

スイッチと聞くと ON/OFF な気がしてしまうけど、デフォルトON でスイッチ自体は押すことで OFF しか発しないってイメージかな  

いろんな機器を繋いでいても結局制御しているのは Arduino 側で Arduino 側のピンしか制御できない  
スイッチに対して制御しているのではなく、スイッチからの入力値を元に Arduino 側で制御しているって理解してやっとわかった  
スイッチからのデジタル入力がどうも見えにくいので中々理解しづらい  

そもそも電子回路におけるデジタル回路とアナログ回路とは何なのか  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://spiceman.jp/analog-digital-circuit/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:spiceman.jp/wp-content/uploads/media-library-folders/circuit-knowledge/what-analog-circuit/analog-digital-circuit/analog-digital-circuit.jpg" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">アナログ回路とデジタル回路の違い｜Spiceman</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">当記事では、「アナログ回路とデジタル回路(ディジタル回路)の違い」について、詳しく解説していきます。 アナログ回路とデジ</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

なんとなくだけど、今回のチュートリアルでスイッチを利用する場合、 Arduino のデジタル回路側ピンを使った理由がわかる  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
