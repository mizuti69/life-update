+++
title = "Raspbisn のセットアップと SSM 導入"
tags = ["AWS","IoT"]
categories = ["SE"]
date = "2020-08-20"
description = "Raspberry Pi Raspbian OS のセットアップと AWS SSM で出来ることの検証"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Raspbian OS のセットアップ
<i class="fas fa-external-link-alt"></i> [Raspberry Pi](https://www.raspberrypi.org/)  

AWS SSM が Raspbian OS に 対応してたので、  
Raspbian のセットアップ復習がてら検証してみる  

### Raspbian OS のインストール  
ラズパイで Raspbian OS を動かすために SD カードに焼く必要があるが、  
現在は RaspberryPi 用のイメージ焼き付けようツールが準備されていて簡単になっていた  

<i class="fas fa-external-link-alt"></i> [Raspberry Pi - Downloads](https://www.raspberrypi.org/downloads/)  

作業端末は Windows なので Windows 用インストーラー `imager_1.4.exe` をダウンロードしインストールする  
起動すると下記のような感じになっており  

<img src="/images/2020/aws/aws-iot/iot-ssm-01.png" />  
<img src="/images/2020/aws/aws-iot/iot-ssm-02.png" />  


インストールする OS の設定、あるいはフォーマット  
書き込む対象のデバイス  

と選択出来る  
これだけあればイメージをダウンロードじて、書き込み用ソフト入れて～とかやらずにシンプルに出来るようになった  
OSのバージョン等々細かく指定できるようになると更に嬉しいかな  

ここでは `Raspberry Pi OS (other)` にある `Raspberry Pi OS LITE` をインストールする  
GUI が無いバージョンでその分OSイメージサイズが小さく、インストールに時間がかからない  

※`Raspberry Pi OS (32-bit)` は Full のイメージで 4GB 相当あるためイメージを焼くだけで 1時間くらいかかる  

SDカードリーダーは無ければ適当に用意しておく  

{{< rawhtml >}} 
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B07PHPK8CP&linkId=ccfa78a91a08d2e73175af9ae78e4285"></iframe>
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B07P1XB7BG&linkId=4c36d4001f2c199d6679c48e504e379a"></iframe>
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=sinokyoufu-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B074B28H3S&linkId=5342d506ef5afd6de27d2c38bbb0786f"></iframe>
{{< /rawhtml >}}

### SSH のデフォルト有効化  
CDカードへのOSイメージ焼き後このままラズパイにセットして起動してもよいが、  
デフォルトでは SSH が有効化されておらず、キーボード、マウス、モニターを用意して・・・と面倒なので  
最初から起動時に SSH を起動するようにしておく  

恐らくデフォルトで無効化されているのは Raspbian のデフォルト OS ユーザー情報はパブリックに公開されていて  
IoT の利用性質上 DMZ ネットワークに置かれることからちゃんとセットアップするまでのリスク回避策でしょう  

先程焼き付けたSDカードをもう一度Windowsに取り付け  

<img src="/images/2020/aws/aws-iot/iot-ssm-03.png" />  

焼き付けると `boot` というデバイス名で認識されるためデバイスを開き、**ssh** という空のテキストファイルを追加しておく  
これだけで起動時にSSHが有効化される  

### 接続と初期セットアップ  
準備できたSDカードでラズパイを起動し、ネットワークにつなげる  
デフォルトで WiFi は有効化されているがプロファイル設定は出来ていないため当然繋がらない  
SSH同様に最初から設定を埋め込む方法も有るみたいだけど、NICが付いているラズパイ3、4 であれば有線LAN繋げればいいだけなので楽  

任意のツールでいいのでローカルネットワーク上のラズパイを検索しIPを特定  
Raspbian のデフォルトOSユーザー `pi` / `raspberry` でSSH接続できればOK  

初期セットアップはいつもどおりに  

* `apt update && apt upgrade`
* 必要なパッケージの追加
* 日本語、日本環境用設定
* `pi` ユーザーのパスワード変更、任意ユーザーの追加
* FW(iptables)設定
* Wifi設定  
* NTP(systemd-timesync)設定  
* SSH設定あれば

手順ややること忘れないように Ansible でプレイブック化しておこうと思う  
`rapi-config` コマンドがあるけどそれだけだとプレイブック化出来ない気がしてるので調べながら  

## AWS SSM 設定

### ハイブリットアクティベーションの有効化  
オンプレミス環境のサーバを SSM 上で管理するためのアクティベーションコードを発行する  

<img src="/images/2020/aws/aws-iot/iot-ssm-04.png" />  

オンプレミス環境のサーバを管理する場合には管理対象が1000台以上を超える場合 アドバンスドプラン に変更する必要がある  
詳しくは料金表を参照  
[AWS Systems Manager の料金](https://aws.amazon.com/jp/systems-manager/pricing/)  

またアクティベーションの有効期限を指定する必要があり、最大でも作成したコードは30日まで有効となり、  
失効後は同一のコードを利用してインスタンスの追加は行えない  
失効後に管理インスタンスを追加したい場合は新規でアクティベーションコードを作成する必要がある  

作成するとコードが表示されるので  

<img src="/images/2020/aws/aws-iot/iot-ssm-05.png" />  

表示された **Activation Code** と **Activation ID** をメモしておいて SSM Agent に登録する  

### SSM Agent のインストール
環境に合わせた手順に則って SSM Agent をインストールする  
[Install SSM エージェント for a hybrid environment (Linux)](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/sysman-install-managed-linux.html)  

```
# mkdir /tmp/ssm
# curl https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/debian_arm/amazon-ssm-agent.deb -o /tmp/ssm/amazon-ssm-agent.deb
# dpkg -i /tmp/ssm/amazon-ssm-agent.deb
# service amazon-ssm-agent stop
# amazon-ssm-agent -register -code "activation-code" -id "activation-id" -region "region"  -y
Error occurred fetching the seelog config file path:  open /etc/amazon/ssm/seelog.xml: no such file or directory
Initializing new seelog logger
New Seelog Logger Creation Complete

2020-08-20 18:29:32 INFO Successfully registered the instance with AWS SSM using Managed instance-id: *************

# service amazon-ssm-agent start
```

インストールと関連付けが成功すると `~/.aws/credentials` にセッション用トークンが登録され  
SSM上もマネージドインスタンスで確認できる  

<img src="/images/2020/aws/aws-iot/iot-ssm-06.png" />  

### セッションマネージャーの利用
マネージドインスタンスへの登録が完了すれば一旦は完了  
試しにセションマネージャーを利用してみる  

<img src="/images/2020/aws/aws-iot/iot-ssm-07.png" />  

オンプレサービスだとインスタンス状態は表示されないらしい  
いざセッションマネージャーを利用しようとすると  

<img src="/images/2020/aws/aws-iot/iot-ssm-08.png" />  

ハイブリッドアクティベーションによって登録されたインスタンスに対してセッションマネージャーを利用する場合  
アドバンストインスタンスプランに変更する必要があり、さらに変更した場合 **同AWSアカウントで管理している全てのマネージドインスタンスも同様のプラン扱いになる**  
アドバンスドプランは先の登録インスタンス数制限にあったように、別途従量課金が発生する  
利用時間ではなく、登録時間なのでインスタンス数が多い場合結構な金額になるため注意が必要  

ここでは確認のためプランを変更して接続してみると無事接続できた（凄い！）  

<img src="/images/2020/aws/aws-iot/iot-ssm-09.png" />  

### スタンダートブランについて
細かくは確認していないが、セッションマネージャーと台数制限を超えたい場合のみアドバンスドプランに変更する必要があるようだ  
プランの変更自体は SSM の管理画面からできるため　変更／無効 を行う運用も考えられそうだがどうでしょうね  

[アドバンストインスタンス層の有効化 (コンソール)](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-managedinstances-advanced.html)  

<img src="/images/2020/aws/aws-iot/iot-ssm-10.png" />  


---

時代は1週して周りめぐり、オンプレサーバに対してどうやってリモートからmンテナンスする？の悩みを IoT でもう一回考え直させられるとは思わなかった  
クラウド前提だからこそ、もう忘れてたけどオンプレサーバーは簡単にメンテナンス出来ないという前提をもう一度思い出して  
監視やオペレーション処理を過去から掘り起こしてこようと思う  

SSM を利用する場合 RunCommand を行うだけであればスタンダードプランで利用できるので一旦この運用で考える  
VPNを入れるとか、方法は色々あるけれど IoT だと Wifi ルータで 個別 GIP も買わないしどうやるのが良いのか勉強しないと  

AWSさーん わかるけどオンプレに対してのセッションマネージャーも無料か、利用時間での従量課金にしてくれー！  

{{< rawhtml >}} 
<div style="border: dashed 1px #ccc;">
<a href="https://www.amazon.co.jp/%E6%94%B9%E8%A8%82%E6%96%B0%E7%89%88-IoT%E3%82%A8%E3%83%B3%E3%82%B8%E3%83%8B%E3%82%A2%E9%A4%8A%E6%88%90%E8%AA%AD%E6%9C%AC-Software-Design-plus%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA/dp/4297106906/ref=as_li_ss_il?_encoding=UTF8&qid=1597922218&sr=8-7&linkCode=li2&tag=sinokyoufu-22&linkId=39ff595ea733eeb42572ae5eacf565f2&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4297106906&Format=_SL160_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li2&o=9&a=4297106906" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}