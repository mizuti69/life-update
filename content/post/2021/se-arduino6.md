+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工作６"
tags = ["iot"]
categories = ["SE"]
date = "2021-02-22"
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

## 超音波センサー

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x Ultrasonic sensor module 
* (4) x F-M wires (Female to Male DuPont wires) 

**超音波センサー**  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.switch-science.com/catalog/6080/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:d2air1d4eqhwg2.cloudfront.net/images/6080/500x500/3c228984-8add-4d1d-b612-402917c829cb.jpg" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">超音波距離センサ HC-SR04 - スイッチサイエンス</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">超音波の反射を利用して対象物までの距離を測る測距センサです。最高精度3 mmで、2 cmから400 cmの物体の距離を接触せずに測定できます。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.keyence.co.jp/ss/products/sensor/sensorbasics/us_info.jsp" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://www.keyence.co.jp/ss/products/sensor/sensorbasics/us_info.jsp" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">超音波センサの概要と特徴 | センサとは.com | キーエンス</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">超音波センサは超音波の反射を利用して距離を測定するセンサです。ここでは超音波センサの概要、検出原理、透明体の検出が可能などの選定ポイントになる特徴をご紹介。キーエンスは製造業の方向けにセンサ選びに役立つ情報をお届けします。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

> センサヘッドから超音波を発信し、対象物から反射してくる超音波を再度センサヘッドで受信します。超音波式センサは、発信から受信までの「時間」を計測することで対象物までの距離を測定しています。

距離を測るセンサなのね  

試験距離=（高レベル時間×音速（340m / s）/ 2  


**構成**  

<img src="/images/2021/se/arduino/tutorial-36.png" />  

センサーのVCCピンを 5V 電源に  
GNDピンをGNDに  
Echoピンを 11番PWMに  
Trigピンを 12番に
センサー基盤側にピンの名称がちゃんと刻印されてるのでわかりやすい  

この時点でなんとなく、Echoピント Trgiピンを操作してなんかするんだなとわかる  

いつもどおりサンプルプログラムをデプロイし  
シリアルモニタを開くと何かしら文字列が流れていく  

<img src="/images/2021/se/arduino/tutorial-37.png" />  

何かしら検知しているようだ  

コードを見てみると  

```
#include "SR04.h"
#define TRIG_PIN 12
#define ECHO_PIN 11
SR04 sr04 = SR04(ECHO_PIN,TRIG_PIN);
long a;

void setup() {
   Serial.begin(9600);
   delay(1000);
}

void loop() {
   a=sr04.Distance();
   Serial.print(a);
   Serial.println("cm");
   delay(1000);
}
```

ライブラリで完全に簡略化されているが、`log a`に`sr04.Distance()`つまり超音波センサーの値を入れてそれをシリアルログ出力している  
`delay(1000);`間隔で  

センサー自体は Trig に HIGH が送られたとき音波を発生させ  
Echo ピンに音波の応答時間を返しているだけだが、その両方の値をライブラリ内で制御、計算しているためコードはこのように簡略化されている  
便利だが、仕組みや動きの理解という点においては不便だ  

センサーを色んな対象物に対して向けることでセンサーからどれくらいの距離にあるかがわかる  
正確な距離を知りたいとなった場合、超音波の理解も必要（検知対象に対して並行に配置、凸凹したものは距離精度が下がる等）だし  
利用する上では色々メリット、デメリットを把握しておく必要がありそう  

## 温度湿度センサー

使用する機器  

* (1) x Elegoo Uno R3
* (1) x DHT11 Temperature and Humidity module
* (3) x F-M wires (Female to Male DuPont wires)

**温度湿度センサー**  

{{< rawhtml >}} 
<div style="text-align: center;;">
<a href="https://www.amazon.co.jp/KKHMF-%E6%B9%BF%E5%BA%A6%E3%82%BB%E3%83%B3%E3%82%B5%E3%83%BC%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB-%E6%B8%A9%E5%BA%A6%E3%82%BB%E3%83%B3%E3%82%B5%E3%83%BC-Arduino%E3%81%A8%E4%BA%92%E6%8F%9B-%E3%83%87%E3%83%A5%E3%83%9D%E3%83%B3%E3%83%A9%E3%82%A4%E3%83%B3%E3%81%A8%E4%BB%98%E5%B1%9E/dp/B010PZZPLS?&linkCode=li2&tag=sinokyoufu-22&linkId=f0151f789f5cf82b77148333b18b62ba&language=ja_JP&ref_=as_li_ss_il" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B010PZZPLS&Format=_SL160_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li2&o=9&a=B010PZZPLS" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}

> DHT11 デジタル温湿度センサーは、温度と湿度の較正されたデジタル信号出力を含む複合センサー
です。専用のデジタルモジュール収集技術と温湿度感知技術を適用し、高い信頼性と優れた長期安定
性を保証します。  

* データシート  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://akizukidenshi.com/catalog/g/gM-15491/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://akizukidenshi.com/catalog/g/gM-15491/" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">ＤＨＴ１１使用温湿度センサモジュール: センサ一般 秋月電子通商-電子部品・ネット通販</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">電子部品,通販,販売,半導体,IC,マイコン,電子工作ＤＨＴ１１使用温湿度センサモジュール秋月電子通商 電子部品通信販売</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

温度湿度センサーは超音波センサー同様になんやかんやして温度と湿度を取得してるらしい  
正直わからん！  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://contents.zaikostore.com/product/4990/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:contents.zaikostore.com/wp-content/uploads/2020/08/HSHCAL001B_eyecatch.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">【アルプスアルパイン】湿度センサの原理 | 半導体・電子部品とは | CoreContents</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">アルプスアルパインの湿度センサについて業界最小サイズのHSHCAL001Bなど、検出原理や特長など詳しく解説していきます。アルプスアルパインの湿度センサは大気中の水蒸気量を検知して電気信号に変換しています。HSHCAL001Bは他社製品と比較しても高湿度、低湿度どちらでも出力電圧が安定していて、高リニアリティが実現可能です。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

**構成**  

<img src="/images/2021/se/arduino/tutorial-38.png" />  

データシートにもある通り、DHT11 は 左から GND、NC、DATA、VDD とピンが設定されていて  
それが基盤に取り付けらモジュール化され左から GND、VDD、DATAとなっている  
※NCは「non connection」 の意味で接続しないピンとなる  

配線し、いつもどおりプログラムとライブラリをデプロイして  
シリアルコンソールを開くとセンサーデータを取得できる  

<img src="/images/2021/se/arduino/tutorial-39.png" />  

T が温度、 H が湿度かな  

コードを見てみると  

```
#include <dht_nonblocking.h>
#define DHT_SENSOR_TYPE DHT_TYPE_11

static const int DHT_SENSOR_PIN = 2;
DHT_nonblocking dht_sensor( DHT_SENSOR_PIN, DHT_SENSOR_TYPE );

/*
 * Initialize the serial port.
 */
void setup( )
{
  Serial.begin( 9600);
}

/*
 * Poll for a measurement, keeping the state machine alive.  Returns
 * true if a measurement is available.
 */
static bool measure_environment( float *temperature, float *humidity )
{
  static unsigned long measurement_timestamp = millis( );

  /* Measure once every four seconds. */
  if( millis( ) - measurement_timestamp > 3000ul )
  {
    if( dht_sensor.measure( temperature, humidity ) == true )
    {
      measurement_timestamp = millis( );
      return( true );
    }
  }

  return( false );
}

/*
 * Main program loop.
 */
void loop( )
{
  float temperature;
  float humidity;

  /* Measure temperature and humidity.  If the functions returns
     true, then a measurement is available. */
  if( measure_environment( &temperature, &humidity ) == true )
  {
    Serial.print( "T = " );
    Serial.print( temperature, 1 );
    Serial.print( " deg. C, H = " );
    Serial.print( humidity, 1 );
    Serial.println( "%" );
  }
}`

```

ライブラリ側で計算しているためこれまたセンサー制御系のコードは無く  
データの定義とデータ取得、表示についてのコードのみ  
`measure_environment` 関数を定義して値を取り続けるようにし、あとは `Loop`で値の取得確認と表示を繰り返している  

実際にセンサーが取得し Arudino 側に送っているデータは 8bit x 5種類となっており  
先頭から湿度（High）、湿度（Loｗ）、温度（High）、温度（Low）となっている  

<img src="/images/2021/se/arduino/tutorial-40.png" />  

チュートリアルの資料だとシリアル出力にビットデータも出してるんだけどコード上はそうなってない  
なのでちょっとコード弄ってみたいなと思ってもコードがあんまり理解できてないから触りにくいなー  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.souichi.club/technology/dht11-datasheet/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:www.souichi.club/wp-content/uploads/dht11-02.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">温湿度センサー（DHT11）の詳しい説明 | そう備忘録</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">温湿度モジュール（DHT11）のより詳しい説明。データシートとDHT11用のモジュールdht11.pyを見比べて分かったことを記事にしている</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

---

マイコンボードでよく取り上げられる超音波センサーと温湿度センサーを触ってみた回  
Arduino という規格、ライブラリやプログラムの抽象化が進んで  
取り付けさえ間違わなければ対して気にせずセンサー自体の値は取得できるし便利なんだけど  

仕組みや原理まで知ろうとすると結構大変だし、深い知識が求められる  
今の自分だととりあえずさわり程度までしかわかんないけど  
そのせいでセンサー繋いだ！値取れた！しか出来てないのがもったいない  

とりあえず今回はここまで  
多分次回に続く  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
