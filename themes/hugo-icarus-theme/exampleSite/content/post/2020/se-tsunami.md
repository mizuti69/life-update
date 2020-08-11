+++
title = "Google の OSS ネットワークセキュリティスキャナー Tsunami を触ってみる"
tags = ["セキュリティ"]
categories = ["SE"]
date = "2020-08-11"
description = "Google のセキュリティスキャナー Tsunami はどんなもんなのか使ってみよう"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Tsunami

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/google/tsunami-security-scanner" data-iframely-url="//cdn.iframe.ly/LrBW0bv"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

現在のステータスは α リリースとのこと  

スキャン概要については下記に記載がある  
https://github.com/google/tsunami-security-scanner/blob/master/docs/orchestration.md  


## Tsunami のインストール  

### 利用準備
以下のツールが必要とのこと  
ネットワークスキャナーとしてはもはや定番  
しかし私の環境は Windows なので docker コンテナを立ててその上で検証しよう  

以下のような DcokerFile を用意し  

```
FROM centos:centos8
```

適当に建てる。OSは好み  
コンテナを起動してログイン  

```
> docker build .
> docker run -d -it <conntener_id> /bin/bash
> docker exec -it <conntener_id> /bin/bash
```

必要なライブラリをインストールする  

```
# yum install epel-release
# yum install -y nmap ncrack java java-devel git
```

導入スクリプトで Java のインストールを JAVA_HOME 変数で判断しているため  
JAVA_HOME 環境変数の設定  

```
# export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.262.b10-0.el8_2.x86_64/jre/
```

## クイックスタートスクリプトの実行  
QuickStart 用の導入スクリプトを実行する  

```
# bash -c "$(curl -sfL https://raw.githubusercontent.com/google/tsunami-security-scanner/master/quick_start.sh)"

BUILD SUCCESSFUL in 2m 1s
14 actionable tasks: 14 executed

Build successful, execute the following command to scan 127.0.0.1:

cd /root/tsunami && \
java -cp "tsunami-main-0.0.2-SNAPSHOT-cli.jar:/root/tsunami/plugins/*" \
  -Dtsunami-config.location=/root/tsunami/tsunami.yaml \
  com.google.tsunami.main.cli.TsunamiCli \
  --ip-v4-target=127.0.0.1 \
  --scan-results-local-output-format=JSON \
  --scan-results-local-output-filename=/tmp/tsunami-output.json
```

中身を見ると Git でソースをクローンしてビルドしている感じだった  

### スキャンの実行
ビルド時の最後にスキャンコマンドの例がでてくる  
が、対象はサンプルアプリがある docker image になっているので少し修正が必要  

またクイックスタート用に最初からインストールされているプラグインは下記のようになっていた  

```
# ls -l tsunami/plugins/
total 340
-rw-r--r-- 1 root root   7154 Aug 11 08:57 exposed_hadoop_yarn_api-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root   6192 Aug 11 08:57 exposed_jenkins_ui-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root   5897 Aug 11 08:57 exposed_jupyter_notebook-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root  41575 Aug 11 08:57 ncrack_weak_credential_detector-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root 267555 Aug 11 08:57 nmap_port_scanner-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root   6247 Aug 11 08:57 wordpress_exposed_installation_page-0.0.1-SNAPSHOT.jar
```

よくあるアプリケーション用のプラグインと nmap みたい  
ドメインを対象にできるオプションがあるか分からないが、サンプルでは IPv4 に対してスキャンできるようなので、  
スキャンをかけても問題ないサイト等を対象に実施してみる  

```
java -cp "tsunami-main-0.0.2-SNAPSHOT-cli.jar:/root/tsunami/plugins/*" \
  -Dtsunami-config.location=/root/tsunami/tsunami.yaml \
  com.google.tsunami.main.cli.TsunamiCli \
  --ip-v4-target=***.***.***.*** \
  --scan-results-local-output-format=JSON \
  --scan-results-local-output-filename=/tmp/tsunami-output.json


Aug 11, 2020 9:16:57 AM com.google.tsunami.main.cli.TsunamiCli run
INFO: Tsunami scan finished, saving results.
Aug 11, 2020 9:16:57 AM com.google.tsunami.common.io.archiving.RawFileArchiver archive
INFO: Archiving data to file system with filename '/tmp/tsunami-output.json'.
Aug 11, 2020 9:16:57 AM com.google.tsunami.main.cli.TsunamiCli run
INFO: TsunamiCli finished...
Aug 11, 2020 9:16:57 AM com.google.tsunami.main.cli.TsunamiCli main
INFO: Full Tsunami scan took 55.12 s.
```

スキャンレポート用の json が出来たようなので中身を見てみると  

```
{
  "scanStatus": "SUCCEEDED",
  "scanStartTimestamp": "2020-08-11T09:16:06.595Z",
  "scanDuration": "50.667s",
  "fullDetectionReports": {
  }
```

まぁ今回は完全に静的なサイトを対象にしたからね・・・  
スキャン中のログを見てると下記のように nmap で何してるかだったり  

```
Executing the following command: '/usr/bin/nmap --unprivileged -Pn -n -sT -sV --version-intensity 5 -T4 --script banner ***.***.***.*** -oX /tmp/nmap5491139303090523058.report'
```

どういう URL に対してリクエストを投げているかなんかも出ている  

---

現時点ではまだスキャンツールとして利用できる！という感じでは無い  
CDNや冗長化が普通のWebシステムにおいてドメインで診断できないのは辛いし（ネットワークスキャナーなのでしょうが無いかな？）  
処理フローのドキュメントにあるように、 nmap でスキャンして HTTP/HTTPS の穴があったらアプリのスキャンもついでにするよ  
という感じなので今後に期待しつつも基本はネットワークスキャンベースかなぁという現状  

Gooleの脆弱性診断ソフトには昔からある skipfish があるので  
その辺といい感じに棲み分けか、統合してくれると使い勝手があるかなという印象  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="https://www.amazon.co.jp/%E4%BD%93%E7%B3%BB%E7%9A%84%E3%81%AB%E5%AD%A6%E3%81%B6-%E5%AE%89%E5%85%A8%E3%81%AAWeb%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AE%E4%BD%9C%E3%82%8A%E6%96%B9-%E7%AC%AC2%E7%89%88%EF%BC%BB%E5%9B%BA%E5%AE%9A%E7%89%88%EF%BC%BD-%E8%84%86%E5%BC%B1%E6%80%A7%E3%81%8C%E7%94%9F%E3%81%BE%E3%82%8C%E3%82%8B%E5%8E%9F%E7%90%86%E3%81%A8%E5%AF%BE%E7%AD%96%E3%81%AE%E5%AE%9F%E8%B7%B5-%E5%BE%B3%E4%B8%B8-%E6%B5%A9-ebook/dp/B07DVY4H3M/ref=as_li_ss_il?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=%E8%84%86%E5%BC%B1%E6%80%A7&qid=1597138156&sr=8-5&linkCode=li3&tag=sinokyoufu-22&linkId=0ae4121050abf035e1453844b84874ca&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=B07DVY4H3M&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li3&o=9&a=B07DVY4H3M" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}
