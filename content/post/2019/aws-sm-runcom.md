+++
title = "AWS SystemManager の メンテナンスウィンドウ + RunCommand で ジョブ管理できるか"
tags = ["aws"]
categories = ["SE"]
date = "2019-09-05"
description = "AWS SystemManager のメンテナンスウィンドウと RunCommand は Cron の代わりとなれるのか検証する"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## SystemManager
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/systems-manager/" data-iframely-url="//cdn.iframe.ly/N6HLoIW?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

AWS上でのサーバ運用においてもはや切り離せなくなってきた感  

### メンテナンスウィンドウ  
前回 SystemManager の <i class="fas fa-external-link-alt"></i> [AWS Patch Manager 検証](/2019/09/03/aws-patch-manager/) をしたときに  
そうかメンテナンスウィンドウで時刻や繰り替え時を設定して、管理対象のサーバにコマンドを実行出来るじゃんと気づくと  
あれ、これジョブ管理出来るじゃんとなったので念の為確認する  

小中規模の、ログ集約やシステム導入するほどでもない環境で cron で自作シェル動かしてたりする場合の選択肢になりえるのか  

### メンテナンスウィンドウで指定できること
cron 書式でジョブ時間の指定、ジョブの実行時間は指定可能  

<img src="/images/2019/aws-sm-command/runcom-01.png" />  

いつからジョブを開始するのか、いつに終了するのか指定可能  

<img src="/images/2019/aws-sm-command/runcom-02.png" />  

### 対象の指定
タグ名かインスタンス名指定かリソースグループから指定可能  

<img src="/images/2019/aws-sm-command/runcom-03.png" />  

### タスクの指定
既存の cron + 自作シェルスクリプト等からの移行を考えるため スクリプト実行を選ぶ  

<img src="/images/2019/aws-sm-command/runcom-04.png" />  

実行対象は先程指定したインスタンス  

<img src="/images/2019/aws-sm-command/runcom-05.png" />  

同時実行の割合や、エラーをどこまで許容するかも指定可能  
ウイルススキャンなど高負荷のジョブは分散できるか  

<img src="/images/2019/aws-sm-command/runcom-06.png" />  

ログや実行通知の設定も可能  

<img src="/images/2019/aws-sm-command/runcom-07.png" />  

試しにここではウイルススキャンのコマンドを登録してみる  

<img src="/images/2019/aws-sm-command/runcom-08.png" />  

試しに設定した時間でコマンド実行の履歴を見てみると無事実行されていた  

<img src="/images/2019/aws-sm-command/runcom-09.png" />  

注意点は標準出力の内容ログには制限があること  

> コマンド出力に表示されるのは 2500 文字までです。コマンドを実行するときに S3 バケットまたは CloudWatch ロググループを指定した場合、完全なコマンド出力は、Amazon S3 または CloudWatch ログで確認することができます。  

詳細にログを出す場合は S3 か CloudWatchLogs を利用しよう  

---

Cron よりはるかに高機能を簡単に利用出来ることがわかった  
これならちょっとしたシステムのジョブはこれで管理完結できるんじゃないか？  
AWS Batch あるけど棲み分け的にはどうなってるんだろ・・・・？  

唯一、追加でほしいとしたら実行ユーザーの指定かな  
基本は root で実行されるようだ  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
