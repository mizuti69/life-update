+++
title = "AWS Cloud Watch Logs 導入検証"
tags = ["aws"]
categories = ["SE"]
date = "2019-07-31"
description = "AWS Cloud Watch Logs を利用して OS のログ管理と監視、保存を自動化する"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Cloud Watch Logs
<i class="fas fa-external-link-alt"></i> [AWS - Amazon CloudWatch Logs とは](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)  

### 料金
<i class="fas fa-external-link-alt"></i> [AWS - Amazon CloudWatch の料金](https://aws.amazon.com/jp/cloudwatch/pricing/)  

ログに関しては 5GB データ (取り込み、ストレージのアーカイブ、Logs Insights クエリによってスキャンされたデータ)は無料利用枠  
CloudWatch では、データ転送受信 (イン) に料金はかからない  
ログの収集、アーカイブ、クエリ単位で料金が発生する  


## ロール権限の設定  
CloudWatch エージェントで、インスタンスのデータを送信できるようにするには、IAM ロールをインスタンスにアタッチする必要があります  
EC2インスタンに割り当てているロールに **CloudWatchAgentServerPolicy** ポリシーを割り当てる  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup01.png" />  

SSMに対する権限も **AmazonSSMFullAccess** 付与しておく  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup02.png" />  

ログ監視したいので **CloudWatchLogsFullAccess** も  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup03.png" />  

## Agent のインストール  
<i class="fas fa-external-link-alt"></i> [CloudWatch エージェントを使用して Amazon EC2 インスタンスとオンプレミスサーバーからメトリクスとログを収集する](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)  

```
# wget https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm
# rpm -ivh amazon-cloudwatch-agent.rpm
```

### 初期設定  

```
# /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```

<i class="fas fa-chevron-circle-right"></i> OSタイプ  

```
=============================================================
= Welcome to the AWS CloudWatch Agent Configuration Manager =
=============================================================
On which OS are you planning to use the agent?
1. linux
2. windows
default choice: [1]:]
1
```

<i class="fas fa-chevron-circle-right"></i> 実行OS環境  

```
Trying to fetch the default region based on ec2 metadata...
Are you using EC2 or On-Premises hosts?
1. EC2
2. On-Premises
default choice: [1]:
1
```

<i class="fas fa-chevron-circle-right"></i> Agent実行ユーザー  

```
Which user are you planning to run the agent?
1. root
2. cwagent
3. others
default choice: [1]:
1
```

<i class="fas fa-chevron-circle-right"></i> デーモン有効可  

```
Do you want to turn on StatsD daemon?
1. yes
2. no
default choice: [1]:
1
```

<i class="fas fa-chevron-circle-right"></i> デーモン利用ポート  

```
Which port do you want StatsD daemon to listen to?
default choice: [8125]

```

<i class="fas fa-chevron-circle-right"></i> デーモンの収集間隔  

```
What is the collect interval for StatsD daemon?
1. 10s
2. 30s
3. 60s
default choice: [1]:
2
```

<i class="fas fa-chevron-circle-right"></i> デーモンによって収集されたメトリックの集約間隔  

```
What is the aggregation interval for metrics collected by StatsD daemon?
1. Do not aggregate
2. 10s
3. 30s
4. 60s
default choice: [4]:

```

<i class="fas fa-chevron-circle-right"></i> デーモンメトリック監視の有効化  

```
Do you want to monitor metrics from CollectD?
1. yes
2. no
default choice: [1]:

```

<i class="fas fa-chevron-circle-right"></i> ホストメトリック監視の有効化  

```
Do you want to monitor any host metrics? e.g. CPU, memory, etc.
1. yes
2. no
default choice: [1]:

```

<i class="fas fa-chevron-circle-right"></i> コア毎のメトリック監視の有効化  

```
Do you want to monitor cpu metrics per core? Additional CloudWatch charges may apply.
1. yes
2. no
default choice: [1]:

```

<i class="fas fa-chevron-circle-right"></i> その他利用可能なメトリクスがある場合全て追加するか  

```
Do you want to add ec2 dimensions (ImageId, InstanceId, InstanceType, AutoScalingGroupName) into all of your metrics if the info is available?
1. yes
2. no
default choice: [1]:

```

<i class="fas fa-chevron-circle-right"></i> メトリックスの即愛知基準を高解像度（分以下）で収集するか  

```
Would you like to collect your metrics at high resolution (sub-minute resolution)? This enables sub-minute resolution for all metrics, but you can customize for specific metrics in the output json file.
1. 1s
2. 10s
3. 30s
4. 60s
default choice: [4]:
4
```

<i class="fas fa-chevron-circle-right"></i> デフォルトメトリクスコンフィグタイプの選択  

| 詳細レベル   | 含められるメトリクス                                                                                                                                                                                                                                                                                                            |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 基本         | Mem: mem_used_percent <br /> Swap: swap_used_percent                                                                                                                                                                                                                                                                                 |
| スタンダード | CPU: cpu_usage_idle、cpu_usage_iowait、cpu_usage_user、 cpu_usage_system <br /> Disk: disk_used_percent、disk_inodes_free <br /> Diskio: diskio_io_time <br /> Mem: mem_used_percent <br /> Swap: swap_used_percent                                                                                                                                 |
| アドバンスト | CPU: cpu_usage_idle、cpu_usage_iowait、cpu_usage_user、 cpu_usage_system <br /> Disk: disk_used_percent、disk_inodes_free <br /> Diskio: diskio_io_time、diskio_write_bytes、diskio_read_bytes、diskio_writes、diskio_reads <br /> Mem: mem_used_percent <br /> Netstat: netstat_tcp_established、netstat_tcp_time_wait <br /> Swap: swap_used_percent   |


```
Which default metrics config do you want?
1. Basic
2. Standard
3. Advanced
4. None
default choice: [1]:
3
```

<i class="fas fa-chevron-circle-right"></i> 確認  

```
Are you satisfied with the above config? Note: it can be manually customized after the wizard completes to add additional items.
1. yes
2. no
default choice: [1]:

```

<i class="fas fa-chevron-circle-right"></i> 移行用設定ファイルが有るか？  

```
Do you have any existing CloudWatch Log Agent (http://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AgentReference.html) configuration file to import for migration?
1. yes
2. no
default choice: [2]:

```

<i class="fas fa-chevron-circle-right"></i> ログ監視の有効化  

```
Do you want to monitor any log files?
1. yes
2. no
default choice: [1]:

```

<i class="fas fa-chevron-circle-right"></i> 監視ログファイルの指定  

```
Do you want to specify any additional log files to monitor?
1. yes
2. no
default choice: [1]:

Log file path:
/var/log/messages

Log group name:
default choice: [messages]

Log stream name:
default choice: [{instance_id}]

```

<i class="fas fa-chevron-circle-right"></i> 設定の確認  

```
Please check the above content of the config.
The config file is also located at /opt/aws/amazon-cloudwatch-agent/bin/config.json.
Edit it manually if needed.
Do you want to store the config in the SSM parameter store?
1. yes
2. no
default choice: [1]:

```

<i class="fas fa-chevron-circle-right"></i> コンフィグの名前  


```
What parameter store name do you want to use to store your config? (Use 'AmazonCloudWatch-' prefix if you use our managed AWS policy)
default choice: [AmazonCloudWatch-linux]

```

<i class="fas fa-chevron-circle-right"></i> 利用リージョン  

```
Which region do you want to store the config in the parameter store?
default choice: [ap-northeast-1]

```

<i class="fas fa-chevron-circle-right"></i> SSMとの連携  

```
Which AWS credential should be used to send json config to parameter store?
1. ASIA3KTK4G3Q3LXILEWM(From SDK)
2. Other
default choice: [1]:

Please make sure the creds you used have the right permissions configured for SSM access.
Which AWS credential should be used to send json config to parameter store?
1. ASIA3KTK4G3Q3LXILEWM(From SDK)
2. Other
default choice: [1]:

```

各種設定ファイル、JSONファイルは下記に保存されている  

```
/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.toml
/opt/aws/amazon-cloudwatch-agent/bin/config.json
```

### Agent の起動

```
# /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s
# systemctl status amazon-cloudwatch-agent.service
● amazon-cloudwatch-agent.service - Amazon CloudWatch Agent
   Loaded: loaded (/etc/systemd/system/amazon-cloudwatch-agent.service; enabled; vendor preset: disabled)
   Active: active (running) since 水 2019-07-31 17:38:44 JST; 39s ago
 Main PID: 6066 (amazon-cloudwat)
   CGroup: /system.slice/amazon-cloudwatch-agent.service
           └─6066 /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent -config /opt/aws/amazon-cloudwatch-age...

 7月 31 17:38:44 study-mng start-amazon-cloudwatch-agent[6066]: /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloud...it.
 7月 31 17:38:44 study-mng start-amazon-cloudwatch-agent[6066]: Valid Json input schema.
 7月 31 17:38:44 study-mng start-amazon-cloudwatch-agent[6066]: I! Detecting runasuser...
 7月 31 17:38:44 study-mng start-amazon-cloudwatch-agent[6066]: 2019/07/31 17:38:44 I! AmazonCloudWatchAgent Vers....0.
Hint: Some lines were ellipsized, use -l to show in full.
```

SSMのパラメータストアにも登録ができていることを確認  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup04.png" />  

## CloudWatchへの反映  
なぜかマニュアルが急に SSM 経由になっているのでそのとおりに  
SSM エージェントのインストールとかは下記のSSMエージェントインストールを参考に  
[AWS SessionManager 検証](/aws-sessionmanager/)  

### ランコマンドの実行  
SSM のランコマンドからコマンドを実行  

<i class="fas fa-chevron-circle-right"></i> コマンドドキュメント： AmazonCloudWatch-ManageAgent  
<i class="fas fa-chevron-circle-right"></i> パラメータ： Optional Configuration Location に パラメータストアに登録した名前を指定  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup05.png" />  

ランコマンドって実際なにやてっるの？はログを見てみると下記の感じらしい  
ちょっと探してみたけど手動で実行する場合の手順がみマニュアル等に見当たらなかった（あっても awslogs agent の古い手順）ので、それはそれで認識しておきたいな  

```
/opt/aws/amazon-cloudwatch-agent/bin/config-downloader --output-dir /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d --download-source ssm:AmazonCloudWatch-linux --mode ec2 --config /opt/aws/amazon-cloudwatch-agent/etc/common-config.toml --multi-config default

Region: ap-northeast-1

credsConfig: map[]

Successfully fetched the config and saved in /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/ssm_AmazonCloudWatch-linux.tmp

Start configuration validation...

/opt/aws/amazon-cloudwatch-agent/bin/config-translator --input /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json --input-dir /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d --output /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.toml --mode ec2 --config /opt/aws/amazon-cloudwatch-agent/etc/common-config.toml --multi-config default

Valid Json input schema.

I! Detecting runasuser...

No csm configuration found.

No structuredlog configuration found.

Configuration validation first phase succeeded

/opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent -schematest -config /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.toml

Configuration validation second phase succeeded

Configuration validation succeeded

----------ERROR-------

2019/07/31 19:52:09 Reading json config file path: /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.d/ssm_AmazonCloudWatch-linux.tmp ...

Redirecting to /bin/systemctl stop amazon-cloudwatch-agent.service

Redirecting to /bin/systemctl restart amazon-cloudwatch-agent.service

```

ちなみに最初下記のようなログが出てうランコマンドが失敗していた  

```
2019/07/31 19:45:49 I! AmazonCloudWatchAgent Version 1.223987.0.
2019/07/31 19:45:49 E! Error parsing /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.toml, open /usr/share/collectd/types.db: no such file or directory
```

とりあえず手動で対象ファイルを作成したら成功したけどいいのかな・・・  

少ししたら CloudWatch 側でログの取得ができていることを確認  
まだエラー出てるけどとりあえず動いてるしいいのかな・・・

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup06.png" />  

ログ自体はずっと保持されてしまうので、ローテーションを設定しておく  

<img src="/images/2019/aws-cloudwatchlogs/cloudwatchlog_setup07.png" />  

とりあえずここまで  
設定までで思いの外時間がかかった、ログの監視や S3 への長期保存とかはまた今度  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}