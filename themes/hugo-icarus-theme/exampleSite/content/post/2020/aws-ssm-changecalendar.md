+++
title = "AWS SystemManager Automation & Change Calendar"
tags = ["aws"]
categories = ["SE"]
date = "2020-01-17"
description = "AWS System Manager Automation & Change Calendar は何者なのか使ってみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## System Manager 
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/systems-manager/" data-iframely-url="//cdn.iframe.ly/N6HLoIW?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金  
[AWS Systems Manager の料金](https://aws.amazon.com/jp/systems-manager/pricing/)  
まだメニュー項目には無いようだけれど、多分無料かな  

## Automation
[AWS Systems Manager オートメーション](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-automation.html)  
Change Claender は Automation によって実行されるメンテナンス処理やタスクをスケジュールに基づいて管理しやすくするためのもの  
まず Automation から始める  

<img src="/images/2020/aws-automation/ssm-auto-01.png" />  

> Systems Manager Automation は、Amazon EC2 インスタンスおよび他の AWS リソースの一般的なメンテナンスとデプロイのタスクを簡素化します。自動化では、以下の操作を行うことができます。
> 自動化ワークフローを構築して、インスタンスおよび AWS リソースを設定し、管理します。
> 独自のカスタムワークフローを作成するか、または AWS によって管理された定義済みのワークフローを使用します。
> Amazon CloudWatch Events を使用して自動化タスクおよびワークフローに関する通知を受信します。
> Amazon EC2 または AWS Systems Manager コンソールを使用して、自動化の進捗状況および実行の詳細を監視します。

もともと メンテナンスウィンドウ + RunCommand があるけど何が違うのか  
RunCommandが単一の処理を実行するメンテナンスの自動化だとしたら、  
Automation は 作業手順の自動化ができるというイメージかな？  

例えば下記のようにデフォルトで用意されているドキュメントを見てみると  
<img src="/images/2020/aws-automation/ssm-auto-02.png" />  

インストール作業やパッチ適応など一連の作業、初期セットアップを定義したドキュメント類が用意されており、  
なんかこの前 ImageBuilder の検証したけど似たようなことを定義して（ansibleとかが良かったなぁ）思った気がする  

### Automation の実行  
どういうものか、試しにテンプレを実行してみる  
実行するのはOSのアップデートドキュメント  
<img src="/images/2020/aws-automation/ssm-auto-04.png" />  

シンプルに実行してみる  
<img src="/images/2020/aws-automation/ssm-auto-05.png" />  

実行をする対象のEC2インスタンスを指定し「実行」すると実行される  
前提として実行対象のサーバには事前に SystemManager の Agent はインストール済み  
<img src="/images/2020/aws-automation/ssm-auto-06.png" />  

S3にレポート出力される処理があるが今回はドキュメントの中に出力先を指定してないので失敗するけどそれ以外は成功する  
<img src="/images/2020/aws-automation/ssm-auto-07.png" />  

対象のEC2はもちろん再起動される  
取得されたであろうEBSのスナップショットはコンソール上は見当たらなかった  
ドキュメントの実行内容を見るとほとんどが Lambda をキックしているため、そこの金額は発生しそう  

ChangeCalender で制御できるのは今の所自作のドキュメントオートメーションだけのようなので、
RunCommand も含めいくつかドキュメントを作ったような思い出があるが、自己所有には無くパッチアップデートのドキュメントをコピーして作成しておく  
<img src="/images/2020/aws-automation/ssm-auto-03.png" />  

## Change Calendar
[AWS Systems Manager Change Calendar](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-change-calendar.html)  

<img src="/images/2020/aws-automation/ssm-cc-01.png" />  

> Systems Manager Change Calendar の利点には次のようなものがあります。  
>
> * 変更を適用する前に確認する  
>   Change Calendar エントリを使用すると、環境に破壊的な影響を及ぼす可能性のある Automation の変更を、適用する前に確認できます。  
> * 適切な時間帯にのみ変更を適用する  
>   Change Calendar エントリを使用すると、イベント期間中に環境を安定に維持できます。たとえば、カンファレンスや公開マーケティングのプロモーションなど、リソースに対する需要が高くなると予想される期間に変更をブロックする Change Calendar エントリを作成できます。カレンダーエントリは、休暇中や祝日中など、管理者サポートが制限されると予想される期間に変更をブロックすることもできます。カレンダーエントリを使用すると、失敗したアクションやデプロイのトラブルシューティングを行うための管理者サポートが制限されている時間以外の、特定の時間帯以外の変更を許可できます。  
> * カレンダーの現在または今後の状態を取得する  
>   Systems Manager GetCalendarState API オペレーションを実行して、カレンダーの現在の状態、指定した時刻の状態、次にカレンダーの状態が変更されるようにスケジュールされている時刻を表示できます。  

うん？  

### カレンダーの作成
カレンダーの作成  
カレンダーのデフォルトタイプを決める  
<img src="/images/2020/aws-automation/ssm-cc-02.png" />  

> [Open by default (デフォルトでオープン)] - カレンダーはオープン (Automation アクションはイベントが開始するまで実行可能) で、関連付けられたイベントの期間中はクローズになります。
> [Closed by default (デフォルトでクローズ)] - カレンダーはクローズ (Automation アクションはイベントが開始されるまで実行不可) で、関連付けられたイベントの期間中はオープンになります。

デフォルトでリソースをロックし指定のカレンダー期間だけオープンするか  
デフォルトでリソースをオープンし指定のカレンダー期間だけロックするか  
という感じかな？  

サービスで考えたときはデフォルトロックにしておいて、変更したいときだけオープンしたほうがいいかな  

<img src="/images/2020/aws-automation/ssm-cc-03.png" />  

例えば平日営業日営業時間はオープンにして土日はロックにしておきたい場合  

<img src="/images/2020/aws-automation/ssm-cc-04.png" />  

## ChangeCalendar の関連付け
作成したコピーオートメーションドキュメントにカレンダー情報を見るようステップの追加を行う  
このようにオートメーションのドキュメントを直接編集する必要があるため今は自作のドキュメントにしか関連付けが出来ない  

ドキュメントの編集画面から追加  

<img src="/images/2020/aws-automation/ssm-cc-05.png" />  

登録する内容は ChangeCalendar の　Details に書いてあるのでコピペする  

<img src="/images/2020/aws-automation/ssm-cc-06.png" />  

## メンテナンスウィンドウに登録
試しに使ってみた Automation ドキュメントをメンテナンスウィンドウに登録して ChangeCalender を適用する  
昔作った [AWS Patch Manager 検証](/2019/09/03/aws-patch-manager/) のメンテナンスウィンドウを再利用し 
タスクにオートメーションのドキュメントを登録する  

ここまで来た時点で思ったことは、
似たような機能や、似たような事をやろうと思うと実現できる機能が SystemManager 上で重複してて逆に複雑化してる気がする  

<img src="/images/2020/aws-automation/ssm-auto-08.png" />  

これでオートメーションがメンテナンスウィンドウの定義に則って実行されるようになる  
ちゃんと平日はアップデートは中止されるか、明日確認してみよ  

---

正直、もっと使いやすく、  
色んな人が作ったりメンテナンス登録しているところを ChangeCalender で管理一括スケジュール管理できるよ！的なものかと思ったら
オートメーションのドキュメントにステップを追加するというバリバリのハードコーディングな感じだったのは残念  
ドキュメントについては作法を勉強するコストも掛かるし、未来に期待という気はしつつも流行るのかなぁ？という気がする  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
