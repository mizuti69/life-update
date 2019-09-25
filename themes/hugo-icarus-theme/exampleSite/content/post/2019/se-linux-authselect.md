+++
title = "Redhat 8 何が変わってどうなったのか確認してみる"
tags = ["OS"]
categories = ["SE"]
date = "2019-09-25"
description = "Redhat 8系の日本語マニュアルが出来てたので以前構築検証した内容を再確認してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Redhat8 
Redhat 8 またコピーOSではる CentOS 8 も恐らく同様に、8系はライフサイクルの管理やらOS管理コマンドに幾つか変更が見られる  
下記は 8.0 が正式リリースされたときに検証してみた内容ですが  
<i class="fas fa-external-link-alt"></i> [Redhat8 / CentOS8 設定手順書](https://mizuti69.github.io/book_configure_centos8/)  

このときはまだ日本語マニュアルが準備されて無く、なんとなくで確認していた幾つかの内容のうち  
authselect コマンド、認証周りの仕組みについてマニュアルを読んで見る  

## Authselectの使用  
<i class="fas fa-external-link-alt"></i> [第1章 AUTHSELECT の使用](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/using_authselect_on_a_red_hat_enterprise_linux_host/using-authselect)  

Redhat 8からは標準で `sssdプロファイル`、`winbindプロファイル`への対応がされていて  
LDAP やら Samba やら準備しなくても行けるぜ～的な感じになている  

> Red Hat は、たとえば、ドメイン内のサービスを使用するために、データベースの LDAP、winbind、または nis を使用してユーザーを認証している場合など、  
> 半集中型の ID 管理環境での authselect の使用を推奨しています。  

　
```
# authselect list
- nis            Enable NIS for system authentication
- sssd           Enable SSSD for system authentication (also for local users only)
- winbind        Enable winbind for system authentication
```

### 基本  
気をつけなければ行けないのは対応したことによりプロファイルを設定し、どのように管理されているのか把握する必要が出てきてしまったこと  

* 現在設定されているプロファイル  

```
# authselect current
プロファイル ID: sssd
有効な機能: なし
```

* 定義ファイル  

```
# vim /etc/nsswitch.conf
passwd:     sss files systemd
group:      sss files systemd
netgroup:   sss files
automount:  sss files
services:   sss files
```

> /etc/nsswitch.conf ファイルの内容は、sssd プロファイルを選択すると、最初の 5 つの項目のうちの 1 つに関する情報が要求されている場合に、システムが最初に sssd を使用することを示しています。  
> sssd キャッシュ、および認証を提供するサーバーで、要求された情報が見つからない、または sssd を実行していないと、システムはローカルファイル (/etc/*) を調べます。  
> たとえば、ユーザー ID に関する情報が要求されると、そのユーザー ID は、最初に sssd キャッシュで検索されます。  そこで見つからない場合は、/etc/passwd ファイルが参照されます。  
> 同様に、ユーザーのグループ所属が要求されると、最初に sssd キャッシュで検索され、そこに見つからない場合に限り、/etc/group ファイルが参照されます。  
> 実際には、ローカルの files データベースが検索されることはありません。唯一の例外は、root ユーザーの場合です。これは、sssd で処理されることはありませんが、files で処理されます。  

### PAM設定のカスタマイズ
<i class="fas fa-external-link-alt"></i> [1.4. 独自のカスタム AUTHSELECT プロファイルの作成とデプロイメント](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/using_authselect_on_a_red_hat_enterprise_linux_host/creating-own-authselect-profile)  

* 認証周りの設定管理は`authselect`を使え  
* 手動でファイルを変更するな  
* 認証プロファイルを理解し適切なものを選べ  

ここまでは基本的に検証した際の手順と差異はなさそうだ  
カスタマイズしたいプロファイルのテンプレートをカスタムプロファイルとして作成し  

```
# authselect create-profile nis-custom --base-on nis --symlink-dconf --symlink=REQUIREMENTS
```

オプション等で変更したい必要なファイルのみを抽出もできる  

> コマンドで --symlink-pam オプションを使用すると、PAM テンプレートが、コピーではなく元のプロファイルファイルへのシンボリックリンクになります。  
> --symlink-meta オプションを使用すると、README、REQUIREMENTS などのメタファイルが、コピーではなく元のプロファイルファイルへのシンボリックリンクになります。  
> これにより、元のプロファイルの PAM テンプレートおよびメタファイルへの今後の更新が、カスタムプロファイルにも反映されます。  

## nftables
もうひとつ nftables も見ておく  
<i class="fas fa-external-link-alt"></i> [第5章 NFTABLES の概要](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/securing_networks/introduction-to-nftables_getting-started-with-nftables)  

> nftables フレームワークは、パケットの分離機能を提供し、iptables ツール、ip6tables ツール、arptables ツール、および ebtables ツールの後継となります。  
> 利便性、機能、パフォーマンスにおいて、以前のパケットフィルタリングツールに多くの改良が追加されました。  

### firewalld への影響
<i class="fas fa-external-link-alt"></i> [第3章 FIREWALLD の使用および設定](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/securing_networks/using-and-configuring-firewalls_securing-networks#log-for-denied-packets_using-and-configuring-firewalls)  

検証したときはダイレクトルールが上手く動かなくて困ったのだが、  
マニュアルを見る限り手法は間違っていなそうなので、バグだったのかやり方に誤りがあったのかも知れない  

> Red Hat は、firewalld を介して管理していない IP セットを使用することは推奨しません。  
> このような IP セットを使用すると、そのセットを参照する永続的なダイレクトルールが必要で、IP セットを作成するカスタムサービスを追加する必要があります。このサービスは、firewalld を起動する前に起動する必要があります。  
> 先に起動しておかないと、firewalld が、このセットを使用してダイレクトルールを追加できません。/etc/firewalld/direct.xml ファイルを使用して、永続的なダイレクトルールを追加できます。  

ただダイレクトルール設定に関するマニュアルは日本語が見当たらない  
これは困った  

## Redhat8 の導入検討について
<iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Frheb.hatenablog.com%2Fentry%2Frhel8ga" style="border: 0; width: 100%; height: 190px;" allowfullscreen scrolling="no"></iframe>

こちらのサイトがライフサイクルについてわかりやすく書いてくれている  
ビジネスにおいて導入を検討する場合、大きな検討事項は下記だろう  

> * マイナーリリースを6ヶ月おきに出荷、メジャーリリースを3年おきに出荷します
> * 同一ソフトウェアの複数バージョンを提供します
>   * 一部のmoduleはサポートが短期間です(2年、3年)
>   * moduleの仕組みを使い、RHELの10年ライフサイクルと同期しない、頻繁なアプリケーションの更新を提供します。
>   * RHELにRed Hat Insightsを同梱し、自動的な診断サービスを利用できます

それに伴い Extended Update Support (EUS) が提供されるのは 8.1, 8.2, 8.4, 8.6, 8.8 予定ということ  
えっ、じゃあ今出てる 8.0, 8.3, 8.5, 8.7 は・・・？今までみたいにとりあえず最新版使っとけでは面倒なことになる可能性がある  

これから RedhatEL OSの導入を検討している場合 直近は 7系を使うか、8.1 が出る2019年末ごろまで様子を見たほうがよさそう  

## CentOS8は？
2019年 9月 25日 今日リリースされた  
<i class="fas fa-external-link-alt"></i> [CentOS Project](https://www.centos.org/)  

今まで通りの Redhat EL クローンであるナンバリングの他に 「CentOS Stream」 というローリングリリース式のOSも開始したようだ（Amazon Linuxみたいね）  

> CentOS Streamは，アップストリームのFedoraと，エンタープライズディストリビューションであるRHELの中間（midstream）に位置するエディションと説明されており，Fedoraで実装されている最先端の技術をRHELより先に導入，あるいはRHELの次期マイナーリリースで実装予定のコードを含めることなどが予定されている。  

これで 8 系OSの検証もしやすくなるかな  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4774184268/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61%2BvBxCb0zL._SL160_.jpg" alt="[改訂新版]プロのためのLinuxシステム構築・運用技術 (Software Design plus)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
