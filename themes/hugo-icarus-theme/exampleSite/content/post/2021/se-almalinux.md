+++
title = "CentOS RHEL互換の AlmaLinux 8.3 Beta を触ってみる"
tags = ["PC"]
categories = ["SE"]
date = "2021-02-05"
description = "CentOS 代替として名乗りを上げた AlmaLinux の Beta が公開されたので触ってみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

## AlmaLinux
<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://wiki.almalinux.org/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://capture.heartrails.com/100x100?https://wiki.almalinux.org/" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">AlmaLinux Wiki | AlmaLinux Wiki</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">The AlmaLinux project documentation</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

CentOS代替として声を上げた AlmaLinux で Beta 版が出た  
情報はGithub上にあり、VagrantでインストールできるようAnsibleスクリプト等も置いてあるのだけれど  
Windows端末上で触って見るので、地道に ISO イメージをダウンロードして触ってみる  

VirtualBox、VMware それぞれサポートしていて  
将来的にはAWS、GCP、Azureぞれそれちゃんとサポートしていくらしい  
**まぁ今の時代、どのOSがいいかというよりクラウドでどのOSがサポートされているかが大事だからね**  
RedHatが従来の無償開発用ライセンスを本番利用可の最大16台までいいよってしたけど結局クラウド側が対応するか？という話なんだし  

## インストール
始め方について詳しくは Wiki のリリースノートに書いてあるし  
Vagrant用スクリプトの設定ファイルから環境情報、ISOを設定していく  
[https://github.com/AlmaLinux/cloud-images/blob/master/almalinux-8.pkr.hcl](https://github.com/AlmaLinux/cloud-images/blob/master/almalinux-8.pkr.hcl)  
仮想環境はVirtualBoxのためにMSLを無効化するの面倒なので VMware で確認する  

インストーラーを起動するとこんな感じ  
<img src="/images/2021/se/almalinux/alma-01.png" />  

CentOSと同じなので違和感ない  

## OS設定周り
CentOS Stream 同様に下記手順を元に確認していく  
[https://mizuti69.github.io/book_configure_centos8/](https://mizuti69.github.io/book_configure_centos8/)  

### ネットワーク
変わらず  

```
# hostnamectl status
   Static hostname: localhost.localdomain
         Icon name: computer-vm
           Chassis: vm
        Machine ID: e612a46c2f98469a9fc36487e93a6e35
           Boot ID: 8c95218a3e884397b0009e1c336107a0
    Virtualization: vmware
  Operating System: AlmaLinux 8.3 Beta (Purple Manul)
       CPE OS Name: cpe:/o:almalinux:almalinux:8.3:beta
            Kernel: Linux 4.18.0-240.el8.x86_64
      Architecture: x86-64
```

OS バージョン情報のファイル名は一応変わってた  

```
# cat /etc/almalinux-release
AlmaLinux release 8.3 Beta (Purple Manul)
```

### カーネルパラメータ
sysctl ファイルはデフォルトでは何も記載されていない  
この辺は Redhat/CentOS のデフォルトとちょっと違うようだがデフォルト値は踏襲してるみたい  
全部は確認してないけど  

### SELinux
デフォルトは Enforcing で同じ  

### Cockpit
前からだっけ？デフォルトでインストールされているようになっていてログイン時にコンソールに  
利用するなら enable にしようね！みたいなメッセージが出てくる  

### firewall
firewalld がデフォルトでインストールされていて、 public ゾーンにデフォルト設定されている  

```
# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens160
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

### 時刻同期
chrony がインストールされ、NTP時刻同期も有効化されているけど chrony がデフォルト起動になってない  
またデフォルトの同期先が当然違うものに変わってる  

```
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
pool 2.cloudlinux.pool.ntp.org iburst
```

### パッケージ管理
dnf がパッケージマネージャーとしてあるのは同じとして  
AlmaLinuxのパッケージは下記のように管理されている  

```
# dnf repolist all
repo id                                          repo の名前                                                      状態
appstream                                        AlmaLinux 8.3 - AppStream                                        有効化
appstream-debuginfo                              AlmaLinux 8.3 - AppStream debuginfo                              無効化
appstream-source                                 AlmaLinux 8.3 - AppStream Source                                 無効化
baseos                                           AlmaLinux 8.3 - BaseOS                                           有効化
baseos-debuginfo                                 AlmaLinux 8.3 - BaseOS debuginfo                                 無効化
baseos-source                                    AlmaLinux 8.3 - BaseOS Source                                    無効化
extras                                           AlmaLinux 8.3 - Extras                                           有効化
extras-debuginfo                                 AlmaLinux 8.3 - Extras debuginfo                                 無効化
extras-source                                    AlmaLinux 8.3 - Extras Source                                    無効化
powertools                                       AlmaLinux 8.3 - PowerTools                                       有効化
powertools-debuginfo                             AlmaLinux 8.3 - PowerTools debuginfo                             無効化
powertools-source                                AlmaLinux 8.3 - PowerTools Source                                無効化

# cat /etc/yum.repos.d/almalinux.repo
[baseos]
name=AlmaLinux 8.3 - BaseOS
baseurl=https://repo.almalinux.org/almalinux/8.3-beta/BaseOS/$basearch/os/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-AlmaLinux
```

試しに vim コマンドをインストールしてみようとすると

```
========================================================================================================================
 パッケージ                    アーキテクチャー      バージョン                          リポジトリー             サイズ
========================================================================================================================
インストール:
 vim-enhanced                  x86_64                2:8.0.1763-15.el8                   appstream                1.4 M
依存関係のインストール:
 gpm-libs                      x86_64                1.20.7-15.el8                       appstream                 39 k
 vim-common                    x86_64                2:8.0.1763-15.el8                   appstream                6.3 M
 vim-filesystem                noarch                2:8.0.1763-15.el8                   appstream                 48 k

トランザクションの概要
========================================================================================================================
インストール  4 パッケージ
```

となっており、  
ふむふむ、CetOS のコピーというより CentOS Stream のコピーなのかな？  
epel リポジトリパッケージは alma のリポジトリにあるみたいなので追加は楽そう  

### システムログ
あれー messages が無いなーと思ったら rsyslog がインストールされてない  
journal は入っているので kernel ログ等は問題なく見れるのだけれど、これだと永続的ではないので消えそう  
ここはちょっと対応が必要そうだけど、安定版が出たときにどうなるかな  
rsyslog が無いから /var/log/ 配下もログファイルが少ないし  
logrotate.d 配下のジョブも無い、利用するソフトウェアによっては動きがどうなるかな  

---

とりあえずインストールして基本設定をざらっと見てみた感じ  
Beta版ということだけど、コピーOSなんで完成度は結構高い感じ  

他の人も同じだと思うけど  
とりあえず CentOS 周りは様子見という人が多いんじゃないかな  
Ubunts 等全く環境が変わるのは面倒くさいが、パッケージリポジトリや開発状況、プラットフォーム側のサポート状況  
そのへんを見ながらどこが最終的に生き残るかなーという状況でしょう  

もし AlmaLinux が人気になったらもう少し使ってみようかな  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
