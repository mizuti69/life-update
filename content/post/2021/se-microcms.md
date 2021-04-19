+++
title = "microCMS クローンを使ってみる"
tags = ["Web"]
categories = ["SE"]
date = "2021-04-19"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

microCMS ブログシステムがオープンソースで公開されたそうなので移行先としてどうなのか触ってみる。  

<!--more-->

## MicroCMS ブログアプリのオープンソース化
詳しくは下記を。  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://blog.microcms.io/open-source-the-blog/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:images.microcms-assets.io/assets/f5d83e38f9374219900ef1b0cc4d85cd/0ef9f68d775848f695c87da004a44b1d/open-source-the-blog-ogp.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">microCMSブログをオープンソース化しました</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">本日、microCMSブログのソースコードをオープンソース化いたしました！リポジトリはこちら。microCMSを利用したJamstack構成のWebサイトはここ1年でだいぶ広まりつつあります。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

これは嬉しい。
記事内にもあるように、ヘッドレスCMSをちゃんとやろうとするとCMSの管理機能、UI、等ガッツリした構築が必要になって、  
あれ？結局 WordPress の前にキャッシュ置いたたり、SSG したほうが楽だなってなってハードルが高い印象だったので、機能があり、使えるものが有るのはカスタマイズや学習目的としても嬉しい。  

## インストール

### 構成要素

* Nuxt（SSG）
* microCMS（コンテンツ）
* Netlify（Hosting, Functions）
* ESLint
* Prettier
* PostCSS
* PWA

Netlify は Vercel でもいけるんかな？ Netlify はすでにこのブログで使ってるのでOK、microCMSのアカウント作成は必要かな。  
結構リッチな機能になっていて個人利用だとオーバースペック感はある。あと Next だと嬉しかったな。  

### microCMS の準備
詳細は [はじめに](https://document.microcms.io/manual/getting-started) より。  

#### アカウント作成
<i class="fa fa-external-link" aria-hidden="true"></i> [料金プラン](https://microcms.io/pricing)  
まず料金体系と各プランによる違い、出来ないことを確認しておく。  
今回は検証なので個人利用で無料の Hobby プランで行うが、個人利用でも本利用、更新頻度が高い場合は注意が必要そう。  
※途中から有料プランへの変更は出来ないので注意  

<i class="fa fa-external-link" aria-hidden="true"></i> [アカウント登録](https://app.microcms.io/)  
上記からアカウント作成。  
メールアドレス、パスワードを入力するとメールアドレス宛に認証メールが届くので、メールの認証コードを入力すれば完了。  

#### サービス情報を入力

* サービス名  
  サービスとは microCMS 上で最上位の概念でプロダクト名や組織名などで登録すると良いとのこと。  
* サービスID  
  管理画面のURLやAPIエンドポイントのサブドメインに設定される値。変更は出来ないためよく考えて設定すること。  
* サービス画像  
  アイコン。  
* プランの選択
  Hobby を選択。  

以上で完了し、専用管理画面のURLが払い出され、クリックすると管理画面に遷移する。  

### APIの作成
microCMSのチュートリアルに戻り、microCMSブログ用のAPIを作成していきます。  

| API名 | エンドポイント | 型 | フィールドID | 表示名 | 種類 |
| :-- | :-- | :-- | :-- | :-- | :-- |
| カテゴリー | categories | リスト形式 | name | 名前 | テキストフィールド |
| 著者 | authors | リスト形式 | name <br> text <br> image | 名前 <br> 自己紹介 <br> 画像 | テキストフィールド <br> テキストエリア <br> 画像 |
| パートナー | partners | リスト形式 | company <br> url <br> description <br> logo | 会社名 <br> 会社URL <br> 説明文 <br> ロゴ | テキストフィールド <br> テキストフィールド <br> テキストエリア <br> 画像 |
| ブログ | blog | リスト形式 | title <br> category <br> toc_visible <br> body <br> description <br> ogimage <br> writer <br> partner <br> related_blogs | タイトル <br> カテゴリー <br> 目次 <br> 本文 <br> 概要 <br> OGP画像 <br> 著者 <br> パートナー <br> 関連記事 | テキストフィールド <br> コンテンツ参照（カテゴリー） <br> 真偽値 <br> リッチエディタ <br> テキストフィールド <br> 画像 <br> コンテンツ参照（著者） <br> コンテンツ参照（パートナー） <br> 複数コンテンツ参照（ブログ） |
| 人気の記事※ | articles | オブジェクト形式 | articles | 人気の記事 | 複数のコンテンツ参照（ブログ） |
| バナー※ | banner | オブジェクト形式 | image <br> url <br> alt | 画像 <br> リンク先 URL <br> 代替テキスト | 画像 <br> テキストフィールド <br> テキストフィールド |

※人気の記事は手動でのデータ入力になります。検証時は空欄のまま「公開」で作業をすすめる。  
※バナーの記事はデータはデータ入力になります。検証時は空欄のまま「公開」で作業をすすめる。  

各定義は API から 右上の「API設定」より各種変更できるため、最後に間違いがないか確認しておく。  

### コンテンツの追加
API を作成しただけでコンテンツは何もない状態なので各 API にそれぞれ適当にコンテンツを追加、作成して公開しておく。  

<img src="/images/2021/se/0319_01.png" />  

## microCMS ブログの用意

### ソースをクローン
<i class="fa fa-external-link" aria-hidden="true"></i> [GitHub - wantainc/microcms-blog](https://github.com/wantainc/microcms-blog)  
上記よりクローンし、インストール  

```
> npm install 
```

### 環境変数の設定 
microCMS との連携 API キー情報を設定  

```
> .env
API_KEY=    # microCMS API Key
SERVICE_ID= # microCMS service ID
GA_ID=      # GA tag
```

検証なので GA は空欄のまま、microCMS の API キーは管理画面、左上の歯車アイコンのところから確認できる  

### ローカル起動
ココまでで上手く出来ていればアプリを起動すると表示できるとのこと。  

```
> npm run dev
```

渡しの場合下記モジュールが足らないメッセージが出て上手く出来なかったので追加インストール。  

```
> npm i chokidar
```

また起動時に、起動するが下記のようなメッセージが大量に出力される。  

```
  1:23  error  Delete `␍`  prettier/prettier
  2:40  error  Delete `␍`  prettier/prettier
  3:1   error  Delete `␍`  prettier/prettier
  4:22  error  Delete `␍`  prettier/prettier
  5:1   error  Delete `␍`  prettier/prettier
  6:55  error  Delete `␍`  prettier/prettier
  7:44  error  Delete `␍`  prettier/prettier
  8:2   error  Delete `␍`  prettier/prettier

✖ 545 problems (545 errors, 0 warnings)
  545 errors and 0 warnings potentially fixable with the `--fix` option.
```

なにやらインデントや改行系？のエラーらしく、メッセージにある通り `--fix` オプションで修正できるらしいので実行。  

```
>  yarn run lint --fix
```

もう一度起動させると  

<img src="/images/2021/se/0319_02.png" />  

出来た！。  

## その他
ココまでであれば Netlify の用意はなくとも確認できる。  
検索機能もあるが、こちらは Netlify-lambda で実現とのことなので確認したい場合はマニュアル参照。  
実際に Web 上で公開していく場合にはマニュアルをみつつ、 Netlify を準備して進めればいい。  

とりあえず使い勝手やイメージが出来たので今回はココまでで良しとする。  
実際に利用したいのであればあとはソースを編集してヘッダーやフッター、デザインを変えたりが必要でしょう。  

今の所記事を書こうと思ったら microCMS 管理画面上で各パーツ等から作成するしか無いので、  
プレビューと加筆のこの辺を手間と感じるのであれば規制品の CMS 使えばいいんじゃないって感じなのかな。  

使い勝手自体は普通の CMS となんら変わりなく使える。  
ちょっとローカル環境の準備に知識がいるくらい。  

ブログの移行先にどうかなと思っているので、もうちょっと触って見るかも。  
