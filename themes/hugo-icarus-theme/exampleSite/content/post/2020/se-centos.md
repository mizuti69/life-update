+++
title = "CentOS RHEL互換の CentOS Linux 終了のお知らせ"
tags = ["PC"]
categories = ["SE"]
date = "2020-12-14"
description = "CentOS Linux終了に備えて CentOS Stream は何が違うのか、代替はどうする？を調べる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

## 「CentOS 8」が2021年末で終了、以後の開発はCentOS Streamに注力する方針が明らかに
<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://gigazine.net/news/20201209-centos-project/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:i.gzn.jp/img/2020/12/09/centos-project/00.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">「CentOS 8」が2021年末で終了、以後の開発はCentOS Streamに注力する方針が明らかに</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Linuxディストリビューションの1つ「CentOS」を開発しているThe CentOS Projectが、軸足を「CentOS 8」から「CentOS Stream」に移すことを明らかにしました。今後、CentOS StreamはRHEL(Red Hat Enterprise Linux)の開発ブランチとして機能していくことになります。一方で、CentOS 8は2021年末をもって終了します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>  

Redhat傘下に入り、今までど変わらいよと言われては来たが  
やっぱり色々難しいところはあったりするのだろう  
今の所ユーザー側の選択肢としては

* CentOS Stream に移行する
* 他のディストリビューションへ移行する  
* 諦める

という感じだろうか  
そもそも CentOS Linux を利用したい目的が RHEL互換で使い慣れていることであり  
それを無償で商用利用できることが利点であったので今後も商用利用を目的とするのであれば RHEL にするのもいい  
クラウドでサブスクライブされているOSを利用する分にはちょっと費用が上がるだけで住むし  

そもそも商用利用が目的でないなら Ubunts とか Stream とかは移行すればいいだけだろう  
知見や慣れの問題  

### CentOS Steam とは？
商用利用で考えた場合、CentOS Stream への移行はちょっと躊躇する  
なぜなら CentOS Stream は以下のような立ち位置だからだ  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://rheb.hatenablog.com/entry/202007-fedora-distribution#CentOS-Stream" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:cdn-ak.f.st-hatena.com/images/fotolife/h/h-kojima/20200723/20200723174247.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Fedora/CentOS Stream/CentOS/RHELの関係性 - 赤帽エンジニアブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">(注) 本記事は、Software Design 2020年6月号に掲載された「月刊Fedora Journal」初出の記事に修正を加えたものです。 Red Hat ソリューションアーキテクトの小島です。 Fedora系列の主要なLinux Distributionとしてよく名前が挙げられる、Fedora, CentOS, RHELに加えて、2019年9月に発表された新しいDistributionであるCentOS Streamの特徴や関係性をご紹介します。 Fedora系列の主要なLinux Distribution Fedora CentOS Stream CentOS Red Hat E…</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

> CentOS Streamは、RHELの将来のマイナーリリース*1を想定した、RHELの開発ブランチのSnapshotとして提供されます。2020年7月時点ではRHEL8のみが対象となっています。  

パッケージ管理をちゃんとやれば大丈夫そうな気もするが  
どうも安定稼働を求める商用利用としては不安が残る  

**しかし基本的な利用感は今までの CentOS Linux 同等だし**  
**CentOS Stream 8、 CentOS Stream 9 とメジャーバージョンは分けられて管理されるようなためやはり今までと大きく変わりはない**  
開発的なパッケージがリポジトリからアップデート、または追加できるという事以外は  

気になるのは脆弱性情報の取り扱い、サイトではフィードバック、連携がよくなるというが  
今まではほぼ互換だったので RHEL で脆弱性が報告されているパッケージやバージョンは同等に CentOS でも問題で  
同等のパッチ等が出てきたため RHEL のサイトだけチェックしておけばよかったが変わるのかな？  
CentOSは当然有償サポートのようなものはないので、いくらRHEL開発のスナップショップ的な立ち位置で今までと変わらないよと言われても  
独自OSバージョン提供になるのであればパッチの適応頻度やバグ、脆弱性情報の集め方はどうしたってズレが出てくるだろう  

あれ？ Fedoraさんは？という気はしないでもないが  

### リリース形態
CentOS Stream の従来との一番の違いは コンセプトからくるローリングリリース形態をとっていること  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://japan.zdnet.com/article/35060029/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:japan.zdnet.com/storage/2015/02/06/130b00962491afb940db3975f77bc819/archlinux_184x138.jpg" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">どちらを選ぶべき？--Linuxのローリングリリースと固定リリース</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Linuxの世界では、リリースモデルとしてローリングリリースの人気が高まっているが、固定リリースのLinuxも依然として存在する。メリットとデメリットがあるそれぞれのリリースモデルのどちらを選ぶべきか。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>  

どちらにもメッリットデメリットがある  
最新のパッケージが早く手に入るということであれば開発、セキュリティ面では嬉しいが  
特定の環境で長期間運用保守を求められるアプリケーションを動かす場合は注意が必要になる  

幸い CentOS Stream メジャーバージョン固定のため RHEL に近いサポート期間が提示されているためそこまで問題は無さそうだ  

## CentOS Stream 8 を実際に触ってみる  
手順書通りに進めてみつつ違いがあるところを確認してみる  
https://mizuti69.github.io/book_configure_centos8/  

> １）久々に Virtualbox 起動したら色々エラーか CentOS Stream のインストーラーが正常に動かないことがあった  
> VirtualBox を 6.0 にダウングレードした所下記のようなエラーが出て  
>
> `Call to WHvSetupPartition failed: ERROR_SUCCESS (Last=0xc000000d/87) (VERR_NEM_VM_CREATE_FAILED).`
>
> どうやら Windows の WLS とかを利用してると Hiyper-Vまわりが阻害されて上手く動かなくなるらしい  
> なのでその場合 下記コマンドを PowerShell 等で実行し、 Hyper-V周りを停止しておく
> 
> ```
> bcdedit /set hypervisorlaunchtype off
> ```
>
> ただしそうすると Docker 利用時とかに困るので使わないときはは 有効化しておいたほうがいい  
>
> ```
> bcdedit /set hypervisorlaunchtype auto
> ```
>
> ２）VirtulaBox 6.1 だとエラーも何も出なくフリーズするため注意（バグかな？）  
> ※昔作ってあった仮想サーバは正常に起動したりしたので対策見つけるまで時間がかかった  
>
> ３）CentOS Stream8 には ネットワークインストーラーの「boot」と従来のフルインストール用「dvd」があるが  
> boot はインストーラー起動設定時のリポジトリエラーが起きてインストール出来ないことがあるため注意  
>
> ４）VirtualBox の場合、正常にインストーラーが完了しない場合がある  
> VMWare Workstation PLayer なら問題なくインストール、起動できた  

どうでもいいけど上記の問題のせいでインストールするだけに 1 日以上かかった  
構成は「最小構成」のパッケージでインストール  


結論としては色々さわってみたけれど大きな違いはなかった  
どちえあかというと仮想化基盤の VirtualBox 、 VMware との相性かなにかが悪いのか  
とにかく起動動作の不具合のほうが多かったので疲れた  

{{< rawhtml >}} 
<div style="text-align: center;;">
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=293&l=ur1&category=amazonrotate&f=ifr&linkID=d69db1f209bc15798210ca62263bede2&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="640" height="100" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0"></iframe>
</div>
{{< /rawhtml >}}
