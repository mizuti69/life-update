+++
title = "静的サイトジェネレーターつらい"
tags = ["hugo","Web"]
categories = ["diary"]
date = "2021-03-23"
description = "静的サイトジェネレータでブログ運用するの辛いよねって話"
menu = ""
banner = "banners/banner_school.jpg"
images = []
+++

<!--more-->


## 静的サイトジェネレータ Hugo を運用していて
Hugoでブログを運用し始めて数年経ったんだけど色々辛くなってきたのでどうしようかなと  

### Hugo をアップデートする
PCを乗り換えしたタイミング等で、PCの乗り換えをスムーズにできるよう  
Chocolatey + Boxstarter でスクリプト化したんだけど  
その際に合わせて Hugo のコマンドもアップデートされた  

特にかわらんやろと思ってたけど、さすがに数年前の Hugo で運用してたのでテンプレートは壊れ  
初期導入時にあまり理解してなく始めたのでテーマのアップデートも簡単にはできず  

※テーマの追加を submodule 化して管理してなかった  
※最初テーマのカスタマイズを理解しておらず、ディレクトリツリーやテーマを直接変更していた  

結構時間がかかってしまった  
つらい  

### リプレイスのマイグレーションが手間  
じゃぁいっそのことテーマごと変えようかなと思ったんだけど、  
Hugo の場合テーマの作り、Post記事のテンプレートが独自に自由が効くため、  
テーマを変えると過去の記事のテンプレート、ヘッダー部分の大幅なマイグレーションが必要になることがわかり断念  

また Hugo は途中のバージョンで MD 内に直接 HTML 記述等が制限されるのがデフォルトになったり、  
同じ Hugo 内での移行さえなかなかままならない、手間がかかる  

この自由度が静的サイトジェネレータの特性、いいところでもあるんだけど、  
ある程度フォーマットを決めててくれないと辛いなーと感じた。  

いっそのこと他のツール Gatsby とかへの移行も考えたんだけど、  
取り急ぎは Hugo 、テーマのアップグレードだけでお茶をにごした。  

つらい  

## 他ツールへの移行検討
静的サイトジェネレータは便利だ。  
カスタマイズが容易で様々な箇所まで手を入れることができ、  
軽く、早く、シンプルだ。  

ただし、これは私の使い方の問題だと思うんだけど、  
きっと頻繁に更新するようなブログ的運用には向いていないんだろう。  

ポートフォリオや、プロダクトのサイト、ティザーサイトとかには向いてるのはわかる。  

でもブログ運用でやりたいのは  

* Markdown等で記述でき  
* 使い慣れたエディタ等でライティングでき  
* Git等で管理運用でき（プラットフォームにできるだけ縛られたくない）  
* ライティングに注力できる環境で  
* プラグインライクに機能を追加できる  

という感じなんだけどなかなか難しい（自分で作れや）  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://jamstack.org/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:www.jamstack.org/img/og/default-og-image.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">For fast and secure sites | Jamstack</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">What is the Jamstack? Why use the Jamstack? How do I get started? Learn what the Jamstack is all about and why it&amp;#39;s the best approach for building faster, more secure websites.</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

### Gatsby
React ベースで、流行的にもいい感じそう  
ただ Hugo と同じ運用の手間を背負う気がしなくもない  
ちょっと触ってみようかな  

### Docusaurus
ドキュメント管理がメインのプロダクトサイト向けだけど、  
今まで Gitbook でしょうがなく管理していたのをまとめる先としてはよさそうだ  
VuePress も同様に良さそうだけど、Facebook が作ってる、React という点でもポイント高い  

### Next.js
サイトジェネレーターに分類されていると「？」って思う  
完全に自分でサイトを作っていきたい！って人には向いてるんじゃないかな？  
サイトを作ることが目的になりそうだし、 React 等の勉強目的だよね  

### Notionブログ
一番気になっている  
まだまだ出てきたばっかりで使うとなると Next.js 導入と同じくらい気合が必要になってしまうけど  
もっと注目が集まって色んなサイトテーマとか  
Notion側でそんな機能が出来てきたら面白いなぁと思う  

## 既存のブログサービスは素晴らしい
当たり前だけど 記事を書くことに集中したいんだったら既存のブログサービスを使ったほうが圧倒的に手間がかからない  
ブログサービスはオワコン化言われて長いけどやはり便利は便利だ  
はてなやZenn等でもいいよね  

あとは自由度やSEOとのトレードオフの話で、いい落とし所を見つけたい・・・

気になるのは、Cloudflare が JAMStack をサポート！とかニュースになってたりはするんだけど  
静的サイトジェネレーターのトレンドとかを Google トレンドで見てみても  
それほど活性化してる気はしないし、Gatsby もそれほどか・・？ってなるし  
Hugo 辛いなーと思いつつもやっぱり人気だし  

やっぱりちょっとしたドキュメンテーションくらいの管理にして  
ブログサービスに出戻りしようかな  
社内 Wiki とか 社内ブログとかだったら引き続きでもいいかもね  
