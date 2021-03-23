+++
title = "ELEGOO Arduino用UNO R3スターターキット で始める マイコン＋電子工作４"
tags = ["iot"]
categories = ["SE"]
date = "2021-02-15"
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
</div>
{{< /rawhtml >}}

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.elegoo.com/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:cdn.shopify.com/s/files/1/0296/9026/5648/files/308ec23709872bfbfd2a2b975b121008_ab56db2e-c179-43c0-b357-9dae066a2b93.jpg?v=1611368475" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">ELEGOO: Best Consumer Level 3D Printers, STEM Kits &amp;amp; Robots</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ELEGOO specializes in the research, development, and production of the most quality &amp;amp; affordable 3D printers, Arduino-based STEM &amp;amp; robotic kits, making technolegy easy-to-access for all the makers around the world.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

前回の続き  
引き続きチュートリアルに沿って電子工作入門していく  

## ブザー

### アクティブブザー

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x Active buzzer 
* (2) x F-M wires (Female to Male DuPont wires) 

**BUZZER**  

<img src="/images/2021/se/arduino/tutorial-21.png" />  

> 電子ブザーは DC 電源であり、集積回路を備えています。  
それらは、コンピュータ、プリンタ、複写機、アラーム、電子玩具、車載電子機器、電話機、タイマ、および音声デバイス用の他の電子製品に広く使用されている。  
ブザーはアクティブなものとパッシブなものに分類できます。  
2 つのブザーのピンを上に向けます。  
緑色の回路基板を持つものは受動的なブザーですが、黒色のテープで囲まれたものはアクティブなブザーです。  

あいかわらす良くわからない説明だが  
アクティブブザーとパッシブブザーというものがあり、各ブザーの違いは端子側にある  

* アクティブブザー  
端子側が樹脂等でコーティングされている  
ブザー内部に振動機があり電力を通すだけでブザー音が鳴る  
よくイメージするブザー  

* パッシブブザー  
端子側が基盤むき出し  
振動器は無いため外部から波形データを渡して鳴らせる必要があります  
イメージ的にはスピーカー  

そして今回のチュートリアルはアクティブブザー  
写真だと正面側しか写してないのがいやらしい  

また今回のチュートリアルで新たに F-M wires という配線ケーブルを使う  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B083LT3759&linkId=d4a46b1566f423e74ffbfd12f81bd763"></iframe>
</div>
{{< /rawhtml >}}


**構成**  
ブザーの端子には＋－があり正面に書いてもあるが端子の足が長いほうが＋、－をGNDにつなぐように注意  

<img src="/images/2021/se/arduino/tutorial-22.png" />  

挿しただけではまだブザーはならないのでサンプルコードを開く  

<img src="/images/2021/se/arduino/tutorial-23.png" />  

コードをデプロイするとちょっと耳障りな音が２音交互に鳴り続ける  

内容自体はブザーの端子片方を繋いだ 12 ピン側を OUTPUT で有効化し  
以降は２つの周波数をループして発振させてるのかな？  

```
void loop()
{
 unsigned char i;
 while(1)
 {
   //output an frequency
   for(i=0;i<80;i++)
   {
    digitalWrite(buzzer,HIGH);
    delay(1);//wait for 1ms
    digitalWrite(buzzer,LOW);
    delay(1);//wait for 1ms
   }
   //output another frequency
   for(i=0;i<100;i++)
   {
     digitalWrite(buzzer,HIGH);
     delay(2);//wait for 2ms
     digitalWrite(buzzer,LOW);
     delay(2);//wait for 2ms
   }
 }
}
```

アクティブブザーの場合発振器は回路内にあるため、発振と停止と繰り返しを工夫することでブザー音をコントーロールしている  
サンプルコード内の for文を減らしたり、増やしたり、 delay 値を変更することで音の変化を確認できると思う  
ただずっと音を聞いてると気分悪くなりそうなので注意  

### パッシブブザー

使用する機器  

* (1) x Elegoo Uno R3 
* (1) x Passive buzzer 
* (2) x F-M wires (Female to Male DuPont wires) 

**パッシブブザー**  

<img src="/images/2021/se/arduino/tutorial-24.png" />  

パッシブブザーは先述したとおり、振動回路を持たないブザー  
ELEGOOのセットはアクティブブザーにシールが張ってあって区別しやすいけどピン側以外は見た目同じ  

**構成**  

<img src="/images/2021/se/arduino/tutorial-25.png" />  

構成自体は前のアクティブブザーと同じ  
ただ端子自体の長さとかで＋－の見分けがつかない  
赤色（正極性）のピン 8 に黒色線（負）を GND に配線します。  

このままでは当然音を発しないのでサンプルコードを開く  

<img src="/images/2021/se/arduino/tutorial-26.png" />  

サンプルコードを見てみるとまず最初に「pitches.h」というモジュールをインクルードしている  
最初のチュートリアルにあったようにライブラリの管理から「pitch」で検索して必要なモジュールがあるか確認し  
無いので同様にサンプルコードと一緒に配置してるライブラリを追加する  

<img src="/images/2021/se/arduino/tutorial-27.png" />  

読み込めると下記のようにライブラリリストに確認できる  

<img src="/images/2021/se/arduino/tutorial-28.png" />  

デプロイすると音階のメロディが聞ける  
コードを見てみよう  

```
int melody[] = {
  NOTE_C5, NOTE_D5, NOTE_E5, NOTE_F5, NOTE_G5, NOTE_A5, NOTE_B5, NOTE_C6};
int duration = 500;  // 500 miliseconds

void loop() {  
  for (int thisNote = 0; thisNote < 8; thisNote++) {
    // pin8 output the voice, every scale is 0.5 sencond
    tone(8, melody[thisNote], duration);
     
    // Output the voice after several minutes
    delay(1000);
  }
   
  // restart after two seconds 
  delay(2000);
}
```

この辺からチュートリアルでコードの説明がなくなる・・・  

`int melody` に配列で ８要素指定し  
それらを `loop` で１配列ずつ `tone` 関数で `delay` を実行して音を鳴らしてるんかな  

メロディの指定はどうやっているのか？  
今回読み込ませたライブラリ内を見てみると下記のように羅列してあり  

```
#define NOTE_B0  31
#define NOTE_C1  33
#define NOTE_CS1 35
#define NOTE_D1  37
#define NOTE_DS1 39
#define NOTE_E1  41
#define NOTE_F1  44
#define NOTE_FS1 46
#define NOTE_G1  49
#define NOTE_GS1 52
#define NOTE_A1  55
#define NOTE_AS1 58
#define NOTE_B1  62

~ 省略  
```

指定した音の定義に対して数字が振られている  
数字がどの音階に対応しているかというと周波数で決まっている  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://www.aihara.co.jp/~taiji/browser-security/js/equal_temperament.html" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://www.aihara.co.jp/~taiji/browser-security/js/equal_temperament.html" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">12平均律と周波数</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;"></span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

例えばコード中の `NOTE_C5` は ライブラリでは値が `523` と設定されていて  
`523`、つまり 523Hz は ド/C5 になる  

---

段々身近なものや、プログラミングで色々できる回路が出てきてぼんやり楽しくなってきた  

とりあえず今回はここまで  
多分次回に続く  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
