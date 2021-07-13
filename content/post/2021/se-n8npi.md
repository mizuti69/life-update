+++
title = "RaspberryPi に N8N をインストールしてみる"
tags = ["Web"]
categories = ["SE"]
date = "2021-07-13"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

在宅ワーク環境を改善していいこうと思い。  

<!--more-->

## N8N
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://n8n.io/" data-iframely-url="//cdn.iframe.ly/gkTQtGg?card=small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

IFTTT や Zapier が有名な、サービス間連携を簡単にできるサービスをオンプレミスな環境にホスティングもできるツール。  
今回はこれを余ってるラズパイにインストールして家の中の連携や在宅ワークの効率化に使えないかな？とインストールしてみる。  

### N8N PI
N8N自体はラズパイをサポートしてないんだけど、ラズパイ用にカスタマイズされたプロジェクトがあったりするのでこれをインストールしてみる。  
<i class="fa fa-external-link" aria-hidden="true"></i> http://n8n-pi.tephlon.xyz/#/  

## ラズパイ側の準備  
既に最低限セットアップされた RaspbianOS があるので割愛。  
n8n-pi にはラズパイ用のイメージも提供されているため、これからイメージを焼いて準備する場合はそちらを利用したほうが楽。  
<i class="fa fa-external-link" aria-hidden="true"></i> [n8n-pi - Download and Prep the Image](http://n8n-pi.tephlon.xyz/#/Install?id=download-and-prep-the-image)  


## N8N-PI のインストール
ここでは n8n-pi の DIY インストール手順に沿って実施ていいく。  

### Node.js のインストール  
Debianしかり標準リポジトリで Nodejs をインストールするとちょっと古いバージョンになってしまうため、いつもの手順で最新にする。  

```
# apt install nodejs npm
# npm install -g n
# n stable
# apt remove nodejs npm
```

### インストールスクリプト実行  
DIYインストール用のスクリプトが用意されているため実行する。  
<i class="fa fa-external-link" aria-hidden="true"></i> https://community.n8n.io/t/n8n-installation-script-for-raspberry-pi-n8n-pi/1392  

```
# wget --no-cache -O - https://raw.githubusercontent.com/TephlonDude/n8n-pi/master/scripts/build-n8n-pi-1.sh | bash
```

セットアップは２セットに分かれていて、最初のスクリプトはラズパイ環境のセットアップや環境調査になっている。  
スクリプト実行により使っているラズパイが動作対象としてOKとなると`n8n`ユーザーがOSに作成され、２セット目のスクリプトがユーザーディレクトリに作成される。  

n8n ユーザーの初期パスワードは下記。  

> n8n  
> n8n=gr8!

２セット目のスクリプトは 一度 n8n ユーザーでログインし直して実行する。  
注意点として、スクリプト途中の apt install nodejs で低いバージョンをインストールされてしまうためコメントアウトしてから実行する。  
デフォルトの `pi` ユーザーは削除されたりするため、**不都合な記述が無いかスクリプトの中身は一度見ておいたほうが良い。**  

```
$ ./build-n8n-pi-2.sh
```

途中 pm2 コマンドが無いと出てくるので手動でインストール。Nodejs アプリのデーモン化ツールだね。  

```
# npm install pm2 -g
```

スクリプトは再実行性がない作りなので、再実行する場合ユーザーが既に作られていたり、ディレクトリがあったりするとエラーで止まるため適宜調整が必要  
インストールが完了すると色々ログイン時のプロンプトが変わってる（笑  

### サービスの起動
n8n 本体はどこにあるのかというと、n8n ユーザーの `.profile` に下記のように PATH が追加されている。  

```
export PATH=~/.nodejs_global/bin:$PATH
```

起動等はコマンド直の他 pm2 経由で起動／停止操作する。  

```
$ pm2 start n8n
$ pm2 stop n8n
```

### ポート変更
n8n のデフォルトリスニングポートは 5678 になってて任意のポートで起動したい場合は環境変数に設定する。  
<i class="fa fa-external-link" aria-hidden="true"></i> https://docs.n8n.io/getting-started/installation/advanced/configuration.html  

PATH 設定同様に `.profile` に追記しておくと良いかも。  

```
export N8N_PORT=1880
```

80番ポート等で起動したい場合は root アカウントで起動する必要があるので、その場合は同様に root の `.profile` に追記しておく。  
