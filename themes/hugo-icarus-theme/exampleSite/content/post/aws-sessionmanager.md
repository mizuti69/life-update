+++
title = "AWS SessionManager 検証"
tags = ["aws"]
categories = ["SE"]
date = "2019-07-24"
description = "AWS SessionManager で外部からアクセス設定等をせずに SSH/SCP が出来るようになり、OSユーザーの指定も出来るようになったので検証してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS SessionManagerのアップデート内容  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/about-aws/whats-new/2019/07/session-manager-launches-run-as-to-start-interactive-sessions-with-your-own-operating-system-user-account/" data-iframely-url="//cdn.iframe.ly/m2bW5CC?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/about-aws/whats-new/2019/07/session-manager-launches-tunneling-support-for-ssh-and-scp/" data-iframely-url="//cdn.iframe.ly/Igw93uO?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

## 概要  
<i class="fas fa-external-link-alt"></i> [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/session-manager.html)  

## SEssionManagerの開始
### SSM Agentのインストール
<i class="fas fa-external-link-alt"></i> [Amazon EC2 Linux インスタンスに SSM エージェント を手動でインストールする](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/sysman-manual-agent-install.html)  

AmazonLinuxならデフォルトでインストールされている  

```
# systemctl status amazon-ssm-agent
● amazon-ssm-agent.service - amazon-ssm-agent
   Loaded: loaded (/usr/lib/systemd/system/amazon-ssm-agent.service; enabled; vendor preset: enabled)
   Active: active (running) since 火 2019-07-16 19:49:21 JST; 2 days ago
 Main PID: 2524 (amazon-ssm-agen)
   CGroup: /system.slice/amazon-ssm-agent.service
           mq2524 /usr/bin/amazon-ssm-agent
```

SessionManager で利用するユーザーがOS上に存在するか確認し、居ない場合 Agent を再起動すると作られる  

```
# getent passwd ssm-user
# systemctl restart amazon-ssm-agent
# getent passwd ssm-user
ssm-user:x:10002:10002::/home/users/ssm-user:/bin/bash
```

### SSM権限の追加
EC2 インスタンスに に割り当てているロールに SSM 権限を追加  
<br />
<img src="/images/2019/aws-ssm/sm_sm_setup01.png" />  

### コンソール上からの SSM 疎通確認
<br />
<img src="/images/2019/aws-ssm/sm_sm_setup02.png" />  

新しいタブが開いてサーバに接続できる  
<br />
<img src="/images/2019/aws-ssm/sm_sm_setup03.png" />  

デフォルトの `ssm-user` には `sudo` 権限があり root 化も可能  
下記のように設定されている  

```
sh-4.2$ id
uid=10002(ssm-user) gid=10002(ssm-user) groups=10002(ssm-user)

# cat /etc/sudoers.d/ssm-agent-users
# User rules for ssm-user
ssm-user ALL=(ALL) NOPASSWD:ALL
```

デフォルトだと IAM アカウントを持っていて、 EC2 に SSM 権限付与していると誰でも接続できてしまうのでIAM アカウントの管理は大事になる  
どのサーバにアクセスさせるか等の権限制御は IAM 単位で設定できる  
S3 や CloudWatch を設定しておけば 操作ログの取得や利用時の通知などできる？  

### ログインユーザーの指定
SSM 接続時のログインユーザーは RunAs を有効化し、SSM 権限を付けたロールか IAM ユーザーにタグをつけることで指定できる  
SessionManager RunAs の有効化、ログインしたい OSユーザーを指定  
<br />
<img src="/images/2019/aws-ssm/sm_sm_setup04.png" />  

ロールにログインしたいOSユーザーでタグを指定  
<br />
<img src="/images/2019/aws-ssm/sm_sm_setup05.png" />  

確認  
<br />
<img src="/images/2019/aws-ssm/sm_sm_setup06.png" />  

ちゃんと変わってる  

## リモートのWindowsクライアントから接続する  
### SessionManagerPluginの利用
AWS CLI と AWS SessionManagerPlugin をインストールする  
<i class="fas fa-external-link-alt"></i> [AWS CLI 用の Session Manager Plugin をインストールする](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html#install-plugin-windows)  

```
> aws --version
aws-cli/1.16.198 Python/3.7.4 Windows/10 botocore/1.12.188

> session-manager-plugin

The Session Manager plugin was installed successfully. Use the AWS CLI to start a session.

> ssh -V
OpenSSH_for_Windows_7.6p1, LibreSSL 2.6.4
```

profile  

```
> aws configure list
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile                <not set>             None    None
access_key     ******************** shared-credentials-file
secret_key     ******************** shared-credentials-file
    region           ap-northeast-1      config-file    ~/.aws/config
```

ユーザーHomeにSSH設定ファイルを作成  

```
> C:\Users\username\.ssh\config
host i-* mi-*
    ProxyCommand C:\Users\username\AppData\Local\Programs\Python\Python37\Scripts\aws.cmd ssm start-session --target %h --document-name AWS-StartSSHSession --parameters "portNumber=%p"
```

### 接続  
```
> ssh usernamei@i-instansid
The authenticity of host 'i-instansid (<no hostip for proxy command>)' can't be established.
ECDSA key fingerprint is SHA256:n+Sh9wthRD+toq4g1wshn3MQBEHSygY+HxJYFz0LUlM.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'i-instansid' (ECDSA) to the list of known hosts.
Password:
Password:
Last failed login: Thu Jul 18 22:01:43 JST 2019 from 127.0.0.1 on ssh:notty
There was 1 failed login attempt since the last successful login.
Last login: Thu Jul 18 22:00:04 2019 from 172.20.2.235

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
1 package(s) needed for security, out of 1 available
Run "sudo yum update" to apply all updates.

[usernamei@localhost ~]$
[usernamei@localhost ~]$
[usernamei@localhost ~]$
[usernamei@localhost ~]$ exit
```

セッション、マネージャ側にも記録が残っている  
<br />
<img src="/images/2019/aws-ssm/sm_sm_setup07.png" />  

リモートからでも OUT 通信で SSH が空いていれば簡単につなげることができる  
AWS 側で security group に穴を開ける必要もないし GIP も節約でき、踏み台サーバを用意する必要もない  
IAM 管理が大変そうだけどなかなかに便利。 AWSコンソール上からつなげるだけでも緊急用に良さそう  
