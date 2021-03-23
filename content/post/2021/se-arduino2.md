+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工作２"
tags = ["iot"]
categories = ["SE"]
date = "2021-02-09"
description = "ELEGOO Arduino用UNO R3スターターキット チュートリアル入門"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## ELEGOO Arduino用UNO R3スターターキット レベルアップ チュートリアル付 mega2560 r3 nanoと互換

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=FFFFFF&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B06XF2HZGT&linkId=0f6f39010078d52b6dd7106fceb44609"></iframe>
</div>
{{< /rawhtml >}}

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.elegoo.com/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:cdn.shopify.com/s/files/1/0296/9026/5648/files/308ec23709872bfbfd2a2b975b121008_ab56db2e-c179-43c0-b357-9dae066a2b93.jpg?v=1611368475" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">ELEGOO: Best Consumer Level 3D Printers, STEM Kits &amp;amp; Robots</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ELEGOO specializes in the research, development, and production of the most quality &amp;amp; affordable 3D printers, Arduino-based STEM &amp;amp; robotic kits, making technolegy easy-to-access for all the makers around the world.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

前回の続き  
引き続きチュートリアルに沿って電子工作入門していく  

## 3. LED
LEDの操作は電子回路についての基礎知識学習となる  

### LED点灯

使用する機器  

* (1) x Elegoo Uno R3
* (1) x 5mm red LED
* (1) x 220 ohm resistor
* (1) x 1k ohm resistor
* (1) x 10k ohm resistor
* (2) x M-M wires (Male to Male jumper wires)
* (1) x BREADBOARD MB-102

**ブレッドボード**  

<img src="/images/2021/se/arduino/tutorial-10.png" />  

電子回路の実験や試作をするための板のこと  

> パンフレットには様々なサイズと構成があります。最も単純な種類は、プラスチックブロックの穴の
グリッドです。  
内部には短い列の穴の間を電気的に接続するための金属ストリップがあります。  
2 つの異なるコンポーネントの脚部を同じ列に押し込むと、それらを電気的に結合します。  
中央の深いチャンネルは、そこに接続が途切れていることを示します。  
つまり、チャンネルの両側にある脚をチップを一緒に接続せずに差し込むことができます。  
一部のブレッドボードには、ボードの長辺に沿ってメイングリッドから離れた 2 つのストリップの穴があります。  
これらのストリップは内部のボードを通り、共通の電圧を接続する手段を提供します。  
これらは通常+5 ボルトとグランドのペアになっています。  
これらのストリップはレールと呼ばれ、ボード内の多くのコンポーネントやポイントに電源を接続することができます。  

**LED**  

<img src="/images/2021/se/arduino/tutorial-11.png" />  

> 5mm は LED の直径を意味します。  
他の一般的なサイズは 3mm と 10mm です。  
LED をバッテリまたは電圧源に直接接続することはできません。その理由は、  
1）LED は正と負のリード線を持ち、誤った方向に置かれた場合は点灯しません。  
2）LED は抵抗を使って電流を制限するか「チョーク」する必要があります。  
LED を付属の抵抗器を使用しないと、あまりにも多くの電流が流れ、加熱され、光が発生する「接合部」を破壊するため、ほとんど直ちに破壊される可能性があります。  
どちらが LED の正のリードであり、負のリードであるかを知るには 2 つの方法があります。  
まず、陽性リードは長くなります。  
第 2 に、負のリードが LED の本体に入る場合、LED のケースに対して平坦な縁がある。  
長いリードの横に平坦な側面を持つ LED がある場合は、長いリードが陽性であると認識する必要があります。  

**抵抗器**  

<img src="/images/2021/se/arduino/tutorial-12.png" />  

抵抗器は電気の流れに抵抗します。  
抵抗の値が高いほど、抵抗が大きくなり、流れる電流は少なくなります。  
LED をどれだけの電流が流れているかを制御するために、これを使用して、それがどれだけ明るく輝いているかを制御します。  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://article.murata.com/ja-jp/article/what-is-resistor" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://article.murata.com/ja-jp/article/what-is-resistor" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">抵抗器とは？ | 村田製作所 技術記事</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">株式会社村田製作所の技術記事、電子部品のはたらき「抵抗器とは?」をご紹介します。抵抗器は電気を流れにくくする電子部品です。流れる電気の量を制限したり調整したりすることで、電子回路を適正に動作させる役割をもつ大切な部品です。村田製作所に関する技術記事をご紹介しています。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>  

**構成**  

<img src="/images/2021/se/arduino/tutorial-13.png" />  

Arduino UNO はこのチュートリアルにおいては 5V 電源の役割のみ  
図のようにボードとArduino と ブレッドボードの ＋ーにケーブルをさして電源を通す  
Arduino上の 5Vのポイント GND ピンで それぞれ 5V電源を＋ーに接続  
このまま LEDを指すと先述のとおりLEDは死ぬので抵抗をかませて電流を調整する  

チュートリアルだと抵抗は黄土色の炭素皮膜抵抗器っぽいけど、セットに入ってるのは水色で５本線だったので金属皮膜抵抗器のようだ
220Ω、1kΩ、10kΩそれぞれの抵抗器においての電流の違いによるLEDの発行状態を確認する  
抵抗器はセットにおいて数値の記載があるので間違わないようにはなっているが、読み方について確認しておく  

**220Ωの場合**：赤２　赤２　黒０　黒１（乗数）　金５（許容誤差±）  

A=5/220

**1KΩの場合**：茶１　黒０　黒０　茶１０　金５  

A=5/1000  

**10KΩの場合**：茶１　黒０　黒０　赤１００　金５  

A=5/10000  

赤と茶が見分けにくい！  
LEDの発光量は目に見えて変わり、身の回りのLED光量からどれくらいの電流を流してるかわかってくるし  
消費電力も見えてくる  
なんとなくここまでは中高の化学かなんかで学んだ気がするなぁ～と思い出し  

### RGB LED

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x 830 Tie Points Breadboard 
* (4) x M-M wires (Male to Male jumper wires) 
* (1) x RGB LED 
* (3) x 220 ohm resistors 
* (1) x BREADBOARD MB-102

**RGB LED**  

<img src="/images/2021/se/arduino/tutorial-14.png" />  

> 3 つの LED のそれぞれの明るさを調整することで、パレットにペイントを混ぜるのと同じ方法で色をミックスします。  
これを行う難しい方法は、レッスン 2 で行ったように異なる値の抵抗（または可変抵抗）を使用することですが、それは多くの作業です  
UNO R3 ボードには analogWrite 機能があり、〜でマークされたピンで使用して、適切な LED にさまざまな電力を出力できます。  
RGB LED には 4 本のリード線があります。  
パッケージ内の単一の LED のそれぞれの正の接続に向かう 1 つのリードと、LED の 3 つの負の側面のすべてに接続された単一のリードがあります。  

わかりにくいが、一番長いピンがGNDそれを基準に各色を見極める  

**PWM**  
Arduino の機器説明でも出てきた PWMピン  
上記にもあるように抵抗器でRGB LEDの調整を行うのは出来なくはないが面倒なのでパルス周波数変調の仕組みを利用する  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://techweb.rohm.co.jp/knowledge/dcdc/dcdc_sr/dcdc_sr01/897" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://techweb.rohm.co.jp/knowledge/dcdc/dcdc_sr/dcdc_sr01/897" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">スイッチングレギュレータの基本：PWM(パルス幅変調)・PFM(パルス周波数変調)制御とは ｜ 電源設計の技術情報サイトのTechWeb</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">PWMとはパルス幅変調、PFMとはパルス周波数変調の略で、どちらも電力制御に用いられる変調方式です。この記事では、PWM制御とPFM制御の原理、方式の比較、メリット・デメリット、それぞれの効率特性について解説します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>  

まったくわからないけど、変調器を利用することで制御回路に流す電圧を調整することができる、そのための回路  

> およそ 1/500 秒ごとに、PWM 出力はパルスを生成します。  
このパルスの長さは、'analogWrite'関数によって制御されます。  
したがって、'analogWrite（0）'はパルスをまったく生成せず、'analogWrite（255）'は次のパルスが到着するまですべての時間持続するパルスを生成します。  
その結果、実際には常に出力されます。
analogWrite に 0 から 255 の間の値を指定すると、パルスが生成されます。  
出力パルスが時間の 5％だけ高い場合、駆動しているものは全出力の 5％しか受け取りません。  
しかし、出力が時間の 90％の間 5V であれば、負荷はそれに供給される電力の 90％を得ます。  
そのスピードで LED の点滅が見えないので、明るさが変わっているように見えます。  

電圧と、流す間隔を調整することで LED が点滅し、そうなることで色が変わってるように見えるらしい  
なるほどわからん  

**構成**  

<img src="/images/2021/se/arduino/tutorial-15.png" />  

利用する抵抗は発光させたい明るさによって選択する。チュートリアルでは 220Ω  

次にPWMを制御するために Arduino IDE からプログラムを実行する  
同梱されてるサンプルコードを確認し　　

<img src="/images/2021/se/arduino/tutorial-16.png" />  

ダブルクリックで IDE 起動  

<img src="/images/2021/se/arduino/tutorial-17.png" />  

デプロイするとLEDが虹色に変化しながら点灯する  

コード内の例えば赤色LED制御部分は下記のようになっていて  

```
redValue = 255; // choose a value between 1 and 255 to change the color.
greenValue = 0;
blueValue = 0;

// this is unnecessary as we've either turned on RED in SETUP
// or in the previous loop ... regardless, this turns RED off
// analogWrite(RED, 0);
// delay(1000);

for(int i = 0; i < 255; i += 1) // fades out red bring green full when i=255
{
redValue -= 1;
greenValue += 1;
// The following was reversed, counting in the wrong directions
// analogWrite(RED, 255 - redValue);
// analogWrite(GREEN, 255 - greenValue);
analogWrite(RED, redValue);
analogWrite(GREEN, greenValue);
delay(delayTime);
}
```

上記をそれぞれ Green、Blue でそれぞれ for 文で回して RGBの値を変化させながら loop させている  
赤（赤から緑に値を１ずつ変化）→緑（緑から青に値を１ずつ変化）→青（青から赤に値を１ずつ変化）→ループ  

例えばコードをちょっと変更してループ内に下記を記述してそれ以外コメントアウトすれば  

```
analogWrite(RED, 255);
analogWrite(GREEN, 0);
analogWrite(BLUE, 0);
```

赤色点灯だけさせられる  

---

いわゆるどんな電子工作キットにもある L チカの学習  
PWMの話が出てきたときはもうダメだと思ったが、余計な疑問は持たずにとりあえずチュートリアルを進めて RGB LED で色の調整をコードで操作するのは楽しい  

とりあえず今回はここまで  
多分次回に続く  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
