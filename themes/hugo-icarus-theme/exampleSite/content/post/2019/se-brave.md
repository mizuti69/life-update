+++
title = "セキュアなブラウザー Brave ってどうなのか"
tags = [""]
categories = ["SE"]
date = "2019-08-27"
description = "セキュアブラウザーのニュースで Brave っていうブラウザーを初めて見たのでインストールしてみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Brave ってどんなブラウザー？  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://www.lifehacker.jp/2019/08/the-best-privacy-and-security-focused-web-browsers.html" data-iframely-url="//cdn.iframe.ly/KS7sITV?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

上記記事をみてて、へーそんなブラウザあるんだと思い、ちょっと使ってみる  
Brave ブラウザーの特徴は以下のような感じ  

* トラッカーや広告をブロックする Shild 機能  
* デフォルト設定でセキュア設定を行ってくれるので細かい設定の変更や調整が不要  
* Chromium ベースで使い慣れた UI
* Chromium ベースのため Chrome からの乗り換え、プラグインの移行も簡単  

なんと言っても目玉は強力なアドブロック機能とサードパーティトラキッキング Cookie 等のブロック  
エロサイトとかでよく見るような画面をスライドするウザーい広告等はほとんどブロックしてくれます  

## インストール
PC版は下記からインストール  

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://brave.com/ja/" data-iframely-url="//cdn.iframe.ly/XCo4myd?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

ちなみに Windows だけど、インストールに小難しい手順はない  
インストーラーをダウンロードして実行すればOK  

インストールが完了すると自動で起動した  
アイコンはイケてないな・・・・  

## 初期設定
インストール後初期せていを行います  

<img src="/images/2019/se-brave/brave-01.png" />  
<img src="/images/2019/se-brave/brave-02.png" />  

ブックマークのインポート  
他ブラウザから移行、ブックマークをインポートしたい場合はインポートできる  

<img src="/images/2019/se-brave/brave-03.png" />  

検索エンジンの指定  
デフォルトは Goolge  
でもせっかくセキュアを歌っているブラウザなので DuckDuckGo にしてみる  

<img src="/images/2019/se-brave/brave-04.png" />  

テーマのカスタマイズ  

<img src="/images/2019/se-brave/brave-05.png" />  

好みがあれば変更を  
普段使ってる Chrome と同じように ダークモードにして ホームボタン、ブックマークバーを表示させてる  

<img src="/images/2019/se-brave/brave-06.png" />  

目玉機能 Brave Shilds 有効になってるから、  
そのせいでサイトがうまく見れないときは無効化してねとのこと  
有効、無効はブラウザバーのアイコンから簡単に操作できる  

<img src="/images/2019/se-brave/brave-07.png" />  

「Brave Rewords」  
広告ブロック機能が強力なため大体の広告はブロックされる  
しかし広告収入が無くなるとお気に入りのサイトは運用できなくなりなくなってしまうかも知れない  
そのため本来きちんと広告収入をあげたいサイトにはちゃんと収入を渡せるように Brave Rewords の仕組みを経由してサイト運営者にお金を払うというもの  
Wikipedia がたまーにやっている 募金広告みたいな感じで、広告はブロックするけどお金を渡しますよ的な感じらしい  

とりあえず有効可し参加しておく  

<img src="/images/2019/se-brave/brave-08.png" />  
<img src="/images/2019/se-brave/brave-09.png" />  

以上で初期設定は完了  
完了するとホームページが表示される  

<img src="/images/2019/se-brave/brave-10.png" />  

なにやら ブロックした広告数やなんやかんやがカウントされてる  

## 日本語化  
デフォルトでは英語設定になっているので日本語化する  
スマホ版は自動で日本語化されてたのに・・・・  

右上のハンバーガーメニューより「Settings」を選択  
左メニューの「Additional settings」より「Languages」を選択  

<img src="/images/2019/se-brave/brave-11.png" />  

デフォルトでは 英語のみになっているため、「Add languages」より「Japanese」を追加する  
追加後、追加した言語を TOP に移動させて 「Display Brave in this language」にチェックを入れる  

そうすると「Relaunch」ボタンが出て再起動されると反映されている  

## どんな広告がブロックされるのか  
たとえば広告ペタペタな 2chまとめ系ブログを見てみると  
下記のように赤枠の広告が表示されなくなっている  

<img src="/images/2019/se-brave/brave-12.png" />  

## アプリケーション・プラグイン  
普通に Chrome Web Store が使えるので、普段遣いであれば乗り換えても大きく問題はなさそう  

## 同期設定
ブックマークを書く端末間で同期することも出来る  
Settings の 「同期」より同期チェーンを開始する  

<img src="/images/2019/se-brave/brave-14.png" />  
<img src="/images/2019/se-brave/brave-15.png" />  

デバイスによって同期用の認証方法が選択できる  
「スマホ／タブレット」を選んだ場合 QRコード で認証ができる。ただし発行元端末と同一ネットワーク上にいないと QRコード は発行されなかった  
「コンピュータ」を選んだ場合、英単語の羅列された認証コードが発行され、それを同期したい端末側で入力、認証すればどう着せてい完了となる  

認証完了後下記のように同期端末が確認できる  

<img src="/images/2019/se-brave/brave-16.png" />  

これを見る限り、今のところはブックマークしか同期されないっぽいな  
Chrome みたいに パスワードも集中管理してくれると楽なのだけれど、そこは「LastPass」とかのアプリで保管するしかないかな  

## シークレットモード
Brave にもシークレットウィンドウ（プライベートウィンドウ）がある  
どうでもいいけど言葉を統一してくれ  

通常のシークレットウィンドウ（検索エンジンがデフォルトで DuckDuckGo）にプラスして Tor を使うこともでき  
より秘匿性を高められるようにもなってる  
Tor ブラウザーはその通信速度の遅さが致命的でどうにも使いにくかったけど、こっちだとどうなんだろ  


---

広告をブロックしてくれるため当たり前だけど普通のブラウジングより表示が早い  
普段使い、個人使いのブラウザーはこちらに移行してもいいんじゃないかと感じられた  

ただブラウザゲームについては注意が必要で、
同じ Chromium ベースとはいえトラッカーブロックのせいなのか上手く起動できない物があった  
なので完全移行はできないけど、ネットサーフィンは Brave 、仕事やゲームは Chrome って感じなのか  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4534056443/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/41Do-5YqjQL._SL160_.jpg" alt="Google AdSense マネタイズの教科書[完全版]" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
