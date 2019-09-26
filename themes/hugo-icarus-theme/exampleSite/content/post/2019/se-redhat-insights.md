+++
title = "Redhat Insights を試してみる"
tags = ["linux"]
categories = ["SE"]
date = "2019-09-26"
description = "Redhat Insigths をインストールしてどんなもんなのか使ってみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

## Redhat Insights
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.redhat.com/ja/technologies/management/insights" data-iframely-url="//cdn.iframe.ly/ZdqRHrM?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

みるからに Amazon Inspector 的な感じな気がする  

### インストール
検証用に開発ライセンスで用意した Redhat8 にインストールしてみる  
Redhat と名を関している通り、 RHEL にサブスクライブされている必要がある  
Red Hat Insightsは、Red HatがサポートするすべてのバージョンのRed Hat Enterprise Linuxバージョン6.10以降で使用できます  

インストール手法は Ansible、 Puppet も用意されているけどここでは手動でアナログにインストールしてみる  
<i class="fas fa-external-link-alt"></i> [RED HAT INSIGHTS ON RED HAT ENTERPRISE LINUX (RHEL)](access.redhat.com/products/red-hat-insights/?extIdCarryOver=true&sc_cid=701f2000001OH7JAAW#getstarted)  

デフォルトでは基本インストールされているけどされていない場合はインストールしておく  

```
# dnf install insights-client
```

ちなみにデーモンで動いているのかなー？と見てみると  
確かに systemd にサービスは登録されているけれど  

```
# systemctl status insights-client
● insights-client.service - Insights Client
   Loaded: loaded (/usr/lib/systemd/system/insights-client.service; static; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:insights-client(8)
```

有効化しようとすると「違うよ」と言われる  

```
# systemctl enable insights-client
The unit files have no installation config (WantedBy, RequiredBy, Also, Alias
settings in the [Install] section, and DefaultInstance for template units).
This means they are not meant to be enabled using systemctl.
Possible reasons for having this kind of units are:
1) A unit may be statically enabled by being symlinked from another unit's
   .wants/ or .requires/ directory.
2) A unit's purpose may be to act as a helper for some other unit which has
   a requirement dependency on it.
3) A unit may be started when needed via activation (socket, path, timer,
   D-Bus, udev, scripted systemctl call, ...).
4) In case of template units, the unit is meant to be enabled with some
   instance name specified.
```

insights-client は timer サービスで動いている  

```
# systemctl status insights-client.timer
● insights-client.timer - Insights Client Timer Task
   Loaded: loaded (/usr/lib/systemd/system/insights-client.timer; enabled; vendor preset: disabled)
   Active: active (waiting) since Thu 2019-09-26 16:14:34 JST; 7min ago
  Trigger: Fri 2019-09-27 02:07:00 JST; 9h left
     Docs: man:insights-client(8)
```

中身を見てみると  

```
# cat /usr/lib/systemd/system/insights-client.timer
[Unit]
Description=Insights Client Timer Task
Documentation=man:insights-client(8)
After=network.target

[Timer]
OnCalendar=daily
RandomizedDelaySec=14400

[Install]
WantedBy=multi-user.target
```

どうやら日次で動いてデータを更新しているようだ  

### Insights の実行
Insigths のサブスクライブ関連づけ  
間に Proxy とかある場合は下記設定ファイルに定義しておく  

```
# vim /etc/insights-client/insights-client.conf

proxy=http://hogehoge.com:8080
```

いざ  

```
# insights-client --register
You successfully registered ******************************* to account *********.
Successfully registered host localhost.localdomain
Automatic scheduling for Insights has been enabled.
Starting to collect Insights data for localhost.localdomain

Uploading Insights data.
Successfully uploaded report from localhost.localdomain to account ********.
```

初回はシステム情報を収集しているのか少し時間がかかる  
無事完了していることを確認

```
# insights-client --status
System is registered locally via .registered file. Registered at 2019-09-26T15:49:26.965017
Insights API confirms registration.
```

### 実行結果
解析結果は下記サイトから確認出来る  
<i class="fas fa-external-link-alt"></i> [Insights Console](https://cloud.redhat.com/insights)  

OS自体は開発検証用なので簡単に初期設定しただけのもの  

<img src="/images/2019/se-redhat-insights/redhat_insights01.png" />  

重要なリスクが１件、普通のリスクが４件、低リスクが１件見つかっている  
重要リスクを見てみると  

<img src="/images/2019/se-redhat-insights/redhat_insights02.png" />  
<img src="/images/2019/se-redhat-insights/redhat_insights03.png" />  

CVE情報とナレッジベースへのリンク等脆弱性への情報を確認できる（対応策くらいもリンクじゃ無くて教えてくれや）  

**Topic**  

<img src="/images/2019/se-redhat-insights/redhat_insights04.png" />  

直近のセキュリティ Topic を教えてくれて、管理対象のマシンで対象があれば教えてくれる  

**Inventory**  

<img src="/images/2019/se-redhat-insights/redhat_insights05.png" />  

管理対象ホスト一覧  
サーバ上の推奨設定についてはここでホストを選択すると見ることが出来る

例えば下記の例では SSH の設定についてアドバイスしてくれている  

<img src="/images/2019/se-redhat-insights/redhat_insights07.png" />  
<img src="/images/2019/se-redhat-insights/redhat_insights08.png" />  

**Remediations**  

<img src="/images/2019/se-redhat-insights/redhat_insights06.png" />  

検知された Rules に対して Ansible Playbook を管理実行出来るっぽい  

### 手動更新の仕方  
脆弱性に対して対応したあとに次の日次更新を待つのが気持ち悪い場合は systemd で定義してあるコマンドを実行すればいいかな  

```
# /usr/bin/insights-client --retry 3
Starting to collect Insights data for localhost.localdomain
```

---

<iframe src="https://hatenablog-parts.com/embed?url=https%3A%2F%2Frheb.hatenablog.com%2Fentry%2Finsights" style="border: 0; width: 100%; height: 190px;" allowfullscreen scrolling="no"></iframe>

結局のところ OpenSCAP の redhat ルールを Webベースで見やすくして複数ホストを管理しやすくした感じくらい  
膨大な Redhat のナレッジから推奨パラメータを教えてくれるのは嬉しいところ  

ただクラウド形式で利用者側が直接 Redhat のサブスクリプションライセンスを持たない場合利用できないんじゃないかな？  
そう考えると OpenSCAP 以上で無いのであれば 独自に html レポート出せる OpenSCAP のほうが小回りがきく  

コマンドでサーバサイドでもレポート見れないかなーと思ったけど今々はそのようなコマンドはないようだ  
APIで通信してるみたいだし、公開してくれないかなー  

あと OpenSCAP のときもあったが、 kernel 等脆弱性でアップデートしてもインシデントが解消されない、あるいはまだ対応パッケージが出ていないのは改善してほしい  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4774184268/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61%2BvBxCb0zL._SL160_.jpg" alt="[改訂新版]プロのためのLinuxシステム構築・運用技術 (Software Design plus)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
