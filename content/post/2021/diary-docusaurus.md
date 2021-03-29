+++
title = "技術系ドキュメントの移行"
tags = ["docusaurus","Web"]
categories = ["diary"]
date = "2021-03-29"
description = "ドキュメンテーションや技術系記事を Gitbook から docusaurus へ移行を考える"
menu = ""
banner = "banners/banner_school.jpg"
images = []
+++

<!--more-->

今まで手順書や学習の記録は Gitbook で管理して Github 上で GithubPages で公開してたんだけど、  
複数の Gitbook のテンプレートを変えたり、リポジトリが分散してったり、色々管理が辛かったので移行先を関東する。  

Gitbook からの移行先としていい感じに感じたのは以下の2つ  

* [VuePress](https://vuepress.vuejs.org/)  
* [Docusaurus](https://docusaurus.io/)

複数ドキュメントを一元管理と考えたら、 React 使ってる Docusaurus かなと思い移行する  

ブログ機能もあるんだけど、  
こういうドキュメンテーションSSG はプロダクトサイト向けだったりするので今まで通り雑記的な記事がここで、勉強系やまとめ系は移行しようと思う。  
例えば前まで書いてた Arduino のチュートリアルなんかは移行する。  

移行自体は量が量なのと、やっぱり多少はマイグレーションが必要なのでゆっくりきままにやってきます。  

あとは本ブログも移行したいなぁ・・・  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://bookit-update.netlify.app/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:bookit-update.netlify.app/img/logo.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Hello from Bookit | Bookit</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Description will go into a meta tag in &amp;lt;head /&amp;gt;</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>
