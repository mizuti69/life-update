+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工９"
tags = ["iot"]
categories = ["SE"]
date = "2021-03-09"
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

## NTC Thermometer  

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x LCD1602 Module 
* (1) x 10k ohm resistor 
* (1) x Thermistor 
* (1) x Potentiometer 
* (1) x 830 tie-points Breadboard 
* (18) x M-M wires (Male to Male jumper wires) 

**Thermometer**  

{{< rawhtml >}} 
<div style="text-align: center;;">
<a href="https://www.amazon.co.jp/uxcell-US-SA-AJD-74716-NTC%E3%82%B5%E3%83%BC%E3%83%9F%E3%82%B9%E3%82%BF-50%E5%80%8B%E5%85%A5%E3%82%8A-10KOhm-3950B-5%EF%BC%85-%E6%B8%A9%E5%BA%A6%E3%82%BB%E3%83%B3%E3%82%B5%E3%83%BC-MF52AT-%E3%82%BB%E3%83%A9%E3%83%9F%E3%83%83%E3%82%AF/dp/B00TGQ0MN4?pd_rd_w=duydP&pf_rd_p=3bdb6fc1-e9d8-4377-8da6-91d54a27dbc3&pf_rd_r=6P0BYNXRJQBGW3PPEW0A&pd_rd_r=f530c032-cc5f-4955-a32d-351bfb7a70ca&pd_rd_wg=QHYQP&pd_rd_i=B00TGQ0MN4&psc=1&linkCode=li2&tag=sinokyoufu-22&linkId=4caa25be06b285520089c63d8e7b2969&language=ja_JP&ref_=as_li_ss_il" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B00TGQ0MN4&Format=_SL160_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li2&o=9&a=B00TGQ0MN4" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}

サーミスタはサーマル抵抗で、温度によって抵抗が変化する抵抗。  
サーミスタは温度によって抵抗値が大きく変化するように作られており、1 度あたり 100 オーム以上変化させることができる  
サーミスタには NTC（負の温度係数）と PTC（正の温度係数）の 2 種類がある  
一般に、温度測定には NTC センサーが使用され、PTC はリセッタブルヒューズとしてよく使用される  
温度が上昇すると抵抗が増加し、電流が流れると発熱し、電流を「チョークバック」して回路を保護しスマートフォン等の発熱対策にも用いられる  

<i class="fas fa-external-link-alt"></i> [NTCサーミスタとは？ - 村田製作所](https://www.murata.com/ja-jp/products/thermistor/ntc/basic/ntc)  
NTCサーミスタの抵抗値は、以下のような式で表すことができるため、広く温度センサとして使用されています。  

RT＝R0expB (1/T-1/T0)  

ここで、RTは周囲温度T (K) の時の抵抗値、R0は周囲温度T0 (K) の時の抵抗値、BはB定数と言われる定数。  

<i class="fas fa-external-link-alt"></i> [NTCサーミスタとは？ポジティブとネガティブ～サーミスタの用途と使い分け ～ - jp.rs-online.com](https://jp.rs-online.com/web/generalDisplay.html?id=ideas-and-advice/ntc-thermistors-guide)  
NTCサーミスタは広い温度範囲で抵抗値が一様かつ滑らかに変化する、したがって、温度を値として検出して、制御する用途に適している  
PTCサーミスタは、特定の温度を超えると、急激に抵抗値が大きくなるデジタル的な変化をします。この性質を利用して、過熱時に回路を遮断したり、機器を過電流から保護したりするのに用いる  

実際のB定数は温度で若干変わるので、カタログには25℃と85℃（50℃/100℃等もあり）で実測した値が記載され  
選定の際は、RoとB定数が基本になります。それぞれに許容誤差が規定されており、±1％クラスの精度を持つものもあります。メーカーでは、製品毎の温度と抵抗値を表にしたものをホームページ等で公表しています。  

今回セットに入っているのは MF52D-103f-3950 NTC Thermistor というもの  
<i class="fas fa-external-link-alt"></i> [MF52 Series Temperarure Sensor - http://www.focusens.com/](http://www.focusens.com/include/sensor-fitting-show.php?id=106)  

B値 3950 ±1% で値は 25℃と50℃で計測されたものらしい  
103 は 25℃のときの抵抗値とのこと  

各温度時の抵抗値については計算サイトがあるので参考までに  
<i class="fas fa-external-link-alt"></i> [NTCサーミスタの温度特性（B定数による計算） - keisan.casio.jp](https://keisan.casio.jp/exec/user/1248569182)  

逆に抵抗から温度を求める場合は逆算すればいいわけですね  

1/T = 1/B × ln(Rth/R0) + 1/T0  

**構成**  

<img src="/images/2021/se/arduino/tutorial-47.png" />  

前回の構成ほぼそのままに  
サーミスタと 10kΩ の抵抗器を追加する  

あとはいつもどおりサンプルプログラムをデプロイすると  

<img src="/images/2021/se/arduino/tutorial-48.jpg" />  

'Temp  XX.XX C'というメッセージが表示され、温度が表示されている  

```
#include <LiquidCrystal.h>
int tempPin = 0;
//                BS  E  D4 D5  D6 D7
LiquidCrystal lcd(7, 8, 9, 10, 11, 12);
void setup()
{
  lcd.begin(16, 2);
}
void loop()
{
  int tempReading = analogRead(tempPin);
  // This is OK
  double tempK = log(10000.0 * ((1024.0 / tempReading - 1)));
  tempK = 1 / (0.001129148 + (0.000234125 + (0.0000000876741 * tempK * tempK )) * tempK );       //  Temp Kelvin
  float tempC = tempK - 273.15;            // Convert Kelvin to Celcius
  float tempF = (tempC * 9.0)/ 5.0 + 32.0; // Convert Celcius to Fahrenheit
  /*  replaced
    float tempVolts = tempReading * 5.0 / 1024.0;
    float tempC = (tempVolts - 0.5) * 10.0;
    float tempF = tempC * 9.0 / 5.0 + 32.0;
  */
  // Display Temperature in C
  lcd.setCursor(0, 0);
  lcd.print("Temp         C  ");
  // Display Temperature in F
  //lcd.print("Temp         F  ");
  lcd.setCursor(6, 0);
  // Display Temperature in C
  lcd.print(tempC);
  // Display Temperature in F
  //lcd.print(tempF);
  delay(500);
}
```

コードを見てみるとLCDのの部分については前回と同じくで  
`int tempReading = analogRead(tempPin)` で analog 入力値を取得していて  
それを `double tempK = log(10000.0 * ((1024.0 / tempReading - 1)));` で温度に変換している  
※その下にある `replace` 内にある式のほうが理解はしやすい  

変換した温度はケルビン(K)なので、摂氏(c)に変換している
同様に摂氏(c)を華氏(f)も計算しているが、その後の表示コードの部分でコメントアウトしている  

各温度単位の変換については下記参照  
[温度の単位の換算 - Wikipedia](https://ja.wikipedia.org/wiki/%E6%B8%A9%E5%BA%A6%E3%81%AE%E5%8D%98%E4%BD%8D%E3%81%AE%E6%8F%9B%E7%AE%97)  

loop内にコードを記載しているのは前回同様動的に変動する表示だからだが、  
面白いのは`lcd.print("Temp         C  ")` で 数値の部分と文字の部分をこういう風に表現できるんだという点  

> かなり奇妙なコメントは、ディスプレイの 16 の列を思い出させるのに役立ちます。  
その長さの文字列を実際の読み取りが行われるスペースで印刷することができます。  
ブランクを記入するには、読み取り位置のカーソル位置を設定し、それを印刷します。 

どういうこと！？空白文字は文字じゃなくそのまま セル？的に未入力扱いってことなのかな！？  

またサーミスタからどのようなアナログ入力があるのか気になったので  
コードに書きを追記して見てみる  

```
void loop()
{
  ~省略~
  lcd.setCursor(0, 1);
  lcd.print(tempReading);
  delay(500);
}
```

すると 21℃ のときに **474** という値が出力されつづけていて 温度が上がると値も大きくなる  
これはサーミスタがなにか数値を発しているのではなく、アナログピンに指すことで Arduino がで電圧を読み取っている  
読み取った値は 0 から 1024 の整数値に変換されているため  
実際の電圧は  

474 / 1024 x 5[V] = 2.32[V]  

今回の構成で表すと  

V = R1[抵抗]/(Rth[サーミスタの抵抗]+R1[抵抗])×5[V]  

知りたいのはサーミスタの抵抗なので  

Rth[サーミスタの抵抗]=(V/5[V]-1)×R1[抵抗]  

となり コードの最初の `tempK` の式の一部がサーミスタの電圧を求めてんのかなーって見えてくる  
（あれ？温度が上がると電圧も上がると抵抗も上がりそうだけど、NCT なら下がるはずだよな・・・？）  
このコードを自分で書ける、書き換えられる自信がないが  

サーミスタを触ればあっという間に体温近くまで温度上昇を確認できるし  
冬なのでストーブとかの近くに持っていけば同様に温度の上昇を確認できる  

しかし環境温度を図るのにはあんまり向いてないのかな？感知距離はわりとサーミスタに触れている空気や部分のみのよう  
ただ前に登場した温湿度センサーよりは小型だし、機器の発熱制御や感知に使われるってのはわかりやすい  

一人暮らしくらいの狭い空間なら室内温度計としても問題はなさそう  

---

触り始めると、何かの値を取得するといっても色々選択肢がある事が分かってきて  
何に何が最適か、やっぱり使ってみないとわかんないよなと実感  

あと急に数学の世界が出てきてもうわかんない  

とりあえず今回はここまで  
多分次回に続く  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
