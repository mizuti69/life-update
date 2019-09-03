+++
title = "AWS Patch Manager 検証"
tags = ["aws"]
categories = ["SE"]
date = "2019-09-03"
description = "AWS パッチマネージャーで EC2 インスタンスのパッチアップデート検知と自動適用検証"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS Systems Manager Patch Manager
<i class="fas fa-external-link-alt"></i> [AWS Systems Manager Patch Manager](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-patch.html)  

> AWS Systems Manager Patch Manager は、セキュリティ関連のアップデートと他のタイプのアップデートの両方でマネージドインスタンスにパッチを適用するプロセスを自動化します。Patch Manager を使用して、オペレーティングシステムとアプリケーションの両方にパッチを適用することができます。(Windows Server では、アプリケーションのサポートは、Microsoft アプリケーションの更新に制限されています)。 オペレーティングシステムのタイプ別に、Amazon EC2 インスタンスまたはオンプレミスサーバー、および仮想マシン (VM) にパッチを適用できます。Windows Server、Ubuntu Server、Red Hat Enterprise Linux (RHEL)、SUSE Linux Enterprise Server (SLES)、CentOS、Amazon Linux、Amazon Linux 2 のサポートされているバージョンが対象になります。インスタンスをスキャンし、見つからないパッチのレポートのみを表示できます。または、すべての見つからないパッチをスキャンして自動的にインストールできます。  
> Patch Manager のパッチベースラインには、リリースから数日以内にパッチを自動承認するためのルールと、承認済みパッチおよび拒否済みパッチのリストが含まれています。パッチ適用を Systems Manager のメンテナンスウィンドウタスクとして実行するようスケジュールすることで、パッチを定期的にインストールできます。また、パッチは、Amazon EC2 タグを使用して個別のインスタンスまたは大規模なグループのインスタンスにインストールできます。(タグは、企業内のリソースを識別およびソートするのに役立ちます)。 作成または更新するときに、タグをパッチベースライン自体に追加できます。  

似たようなので Amazon Inspector があるが、こちらは監査のみではなくパッチの適応も行ってくれるって感じかな？  

### 事前準備
前回 SessionManager の検証で SystemManager に登録されているサーバがあるのでコレに対してテストする  
<i class="fas fa-external-link-alt"></i> [AWS SessionManager 検証](/2019/07/24/aws-sessionmanager/)  

### パッチベースラインの作成
<img src="/images/2019/aws-patch/patchmanager_set01.png" />  

Amazon SystemManager よりコンプライアンス、またはパッチマネージャーを選択しパッチベースラインを作成する  

<img src="/images/2019/aws-patch/patchmanager_set02.png" />  
<img src="/images/2019/aws-patch/patchmanager_set03.png" />  

検証環境は AmazonLinux なので、それに対するセキュリティに関するアップデート、特に Medium 以上のものを対象とする  
パッチを当てるかの承認期限は５日とし、アップデートパッチが出たときのコンプライアンス報告のランクは 高 にしておく  

<img src="/images/2019/aws-patch/patchmanager_set04.png" />  

自動で承認、あるいはアップデートがあっても対象外にするパッケージがある場合は定義する  

<img src="/images/2019/aws-patch/patchmanager_set05.png" />  

ベースラインの作成は完了  

### パッチグループの作成
<img src="/images/2019/aws-patch/patchmanager_set06.png" />  

必須では無いっぽいけど、同様のベースラインを適用するインスタンスをひとくくりのグループとして管理できる  
ベースラインを選択し、アクションの「パッチグループの変更」から作成・変更する  

<img src="/images/2019/aws-patch/patchmanager_set07.png" />  

パッチグループ識別用のタグ名を設定し、パッチグループに入れたいインスタンスに `Patch Group` で設定する  

<img src="/images/2019/aws-patch/patchmanager_set08.png" />  

### パッチ適用の設定
<img src="/images/2019/aws-patch/patchmanager_set10.png" />  

パッチマネージャーの管理画面から「パッチ適用の設定」を選択  

<img src="/images/2019/aws-patch/patchmanager_set09.png" />  

パッチを適用するインスタンスには先程作成したパッチグループを指定する  

<img src="/images/2019/aws-patch/patchmanager_set11.png" />  

パッチ適用のメンテナンスウィンドウを設定する  
今回は検証なのでとりあえず 30 分にして結果を見てみる  

<img src="/images/2019/aws-patch/patchmanager_set12.png" />  

スキャンのみか、スキャンとインストールか選択できる  
スキャンのみであれば Amazon Inspector と機能的には変わらなくなる気が・・・  

### 結果確認
パッチパッケージの確認や適応は yum コマンドを SystemManager の コマンド実行から実行している  
実行内容を見てみると、作成したベースラインの定義を元に yum コマンドを実行しているような感じだった  

```
/usr/bin/python3
/usr/bin/python2.7
/usr/bin/python2
/usr/bin/python
/usr/bin/yum
Using Yum version: 3.4.3
Using python binary: 'python2.7'
Using Python Version: Python 2.7.16

...以下処理  
```

処理が完了すると コンプライアンスの欄で結果を確認できる  

<img src="/images/2019/aws-patch/patchmanager_set13.png" />  

インストールを選択していた場合対象サーバは**再起動**されるので注意  
また yum コマンドで実行しているので それ以外、あるいは yum のレポジトリでデフォルト無効化しているリポジトリパッケージは対象にならないだろう  

承認はあくまで ディストリビュータからリリースされて何日後に適用するかどうか程度の話ってことね  

Inspector もそうだけど、通知する手段がぱっと手順の中で出てこなかったので、  
アップデートがあったのか、実行されたのかを判断しずらい  

実運用的にはスキャンのみ実施しておいて、定期的に対象パッケージをチェックして  
必要により手動実行しか無いんじゃないかなーという気がしている  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
