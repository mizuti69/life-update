+++
title = "AWS CLI version 2 へのアップデート"
tags = ["aws"]
categories = ["SE"]
date = "2020-05-01"
description = "AWS CLI 2 が出てたのでアップデートする"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS CLI v2
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/blogs/news/aws-cli-v2-is-now-generally-available/" data-iframely-url="//cdn.iframe.ly/Zqzbzvv?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

CLI v2 のリリース、及び API も変わってたんですね  
知らなかったので、EC2 上の CLI アップデートしてみる  

### アップデート  
<i class="fas fa-external-link-alt"></i> [Linux での AWS CLI バージョン 2 のインストール](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/install-cliv2-linux.html#cliv2-linux-upgrade)  

V2 からインストール方法が変わったこともあり下記のような注意点がある  

> AWS CLI バージョン 1 と 2 では同じ aws コマンド名が使用されます。両方のバージョンがインストールされている場合、コンピュータによって検索パスで最初に見つかったバージョンが使用されます。以前に AWS CLI バージョン 1 をインストールしていた場合は、AWS CLI バージョン 2 を使用するために以下のいずれかを実行することをお勧めします。  
> 
> * 推奨: AWS CLI バージョン 1 をアンインストールし、AWS CLI バージョン 2 のみを使用します。  
> * オペレーティングシステムの機能を使用して、2 つの aws コマンドのいずれかに対して異なる名前のエイリアスまたは sym リンクを作成します。たとえば、Linux および macOS では symbolic links または alias を使用でき、Windows では doskey を使用できます。  

古い方はアンインストールが推奨とのことらしいのですが、
インストール後に不具合が起きて戻すときのことを考えたら念の為残しておきたい気がするので、共存させてみる  

```
# curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
# unzip awscliv2.zip
# cd aws
# cd ./install

You can now run: /usr/local/bin/aws --version

# aws --version
aws-cli/1.16.102 Python/2.7.16 Linux/4.14.133-113.112.amzn2.x86_64 botocore/1.12.92

# exit
```

一瞬でインストールは完了するが、aws コマンド上はまだ古いバージョンを見ている  
これはログイン時の環境情報を見ているためなので、一度再ログインすればパスの更新が反映されてバージョンが変わっている  

```
# aws --version
aws-cli/2.0.10 Python/3.7.3 Linux/4.14.133-113.112.amzn2.x86_64 botocore/2.0.0dev14
```

ちなみに 旧 awscli は pip でインストールしたんですが、 v2 インストール後もちゃんとあります  

```
# /usr/bin/aws --version
aws-cli/1.16.102 Python/2.7.16 Linux/4.14.133-113.112.amzn2.x86_64 botocore/1.12.92
```

※OS標準の python を使っているので、 python、pip のインストール環境によっては別の所にある  


V2 で使える API 情報は下記  
https://awscli.amazonaws.com/v2/documentation/api/latest/index.html  

日本語サイトはまだ V2 に対応してない  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
