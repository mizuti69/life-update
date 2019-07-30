+++
title = "Hugo + Netlify で Webサイトを公開するまで"
tags = ["Hugo"]
categories = ["SE"]
date = "2019-07-19"
description = "Hugo + Netlify で Markdown 記述をサポートし、Git 管理された個人ブログを簡単に無料で公開してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Hugo
Hugoは最も人気のあるオープンソースの静的サイトジェネレータの1つです。その驚くべきスピードと柔軟性で、Hugoは再びウェブサイトの構築を楽しくします。  
<i class="fas fa-external-link-alt"></i> [Hugo](https://gohugo.io/)  

### Hugo のインストール
Windows 10 環境にインストールする  
ワークフォルダを作成  

```
C:\Users\username\Documents\Project\hugo
C:\Users\username\Documents\Project\hugo\bin
C:\Users\username\Documents\Project\hugo\sites
```

Hugo をダウンロードしてワークフォルダの `bin` 配下に展開する  
<i class="fas fa-external-link-alt"></i> [Github - gohugoio/hugo](https://github.com/gohugoio/hugo/releases)  

展開したらパスを通す  

```
> set path "PATH=%PATH%;C:\Users\username59\Documents\Project\hugo\bin
> hugo version
Hugo Static Site Generator v0.55.6-A5D4C82D windows/amd64 BuildDate: 2019-05-18T07:57:00Z
```

### サイト作成
`sites` フォルダに移動し、サイトの初期設定を行う  

```
> hugo new site <site name>
```

テンプレートのダウンロード  
公式テンプレートから好きなテンプレートを選んで `themes` 配下にダウンロードする  
<i class="fas fa-external-link-alt"></i> [Hugo Themes](https://themes.gohugo.io)  

このサイトのテンプレートは下記を使っています  
<i class="fas fa-external-link-alt"></i> [Hugo Themes - Icarus](https://themes.gohugo.io/hugo-icarus/)  

```
> cd themes
> git clone https://github.com/digitalcraftsman/hugo-icarus-theme.git
```

標準テンプレートのドキュメントルートは `<site name>/content/post` だが、カスタムテンプレートによっては訴求面とルートが異なる  
今回採用したテンプレートではドキュメントルートは `<site name>/thems/hugo-icarus-theme/exampleSite/content` になっている  

### サイトの起動  
Hugoはコマンド実行カレントフォルダの構成を見てコンテンツを表示する。
カスタムテーマのサイトフォルダに移動して起動すればいい  

```
> cd hugo-icarus-theme\exampleSite
> hugo server -D
```

`locahost:1313` にアクセスするとテンプレートのデフォルトサイトが表示される  

### 初期設定
この状態ではサイトの情報がテンプレート情報のままであるため、設定を自分の情報に修正します  
テーマフォルダにある `config.toml` を修正  

```
baseurl = "https://life-update.netlify.com/"
disqusShortname = ""
title = "Life update"

[params]
    author = "mizuti"
    bio = "そこらへんの人"
    location = "Japan"
    avatar = "css/images/avatar.png"
    gravatar = "<gravatar account mail address>"

[params.profile]
    follow_button = ""

[social]
    github          = ""
    twitter         = ""
```

<i class="fas fa-chevron-circle-right"></i> baseurl  
サイトのURL、後の手順で作成する Netlify で公開するサイトのURLを設定  
デフォルトでは `*.netlify.com` のドメイン形式になるためサブドメインに設定したい名前を指定  

<i class="fas fa-chevron-circle-right"></i> disqusShortname
コメントフォーム DISQUS のアカウント名  
とりあえずは空欄にする  

<i class="fas fa-chevron-circle-right"></i> title  
サイトの名前  

<i class="fas fa-chevron-circle-right"></i> author  
サイト管理者の名前  

<i class="fas fa-chevron-circle-right"></i> bio  
サイト管理者の説明文（任意）  

<i class="fas fa-chevron-circle-right"></i> location  
サイト管理者の住所（任意）  

<i class="fas fa-chevron-circle-right"></i> avatar  
サイト管理者のアバター画像  

<i class="fas fa-chevron-circle-right"></i> gravatar  
Gravatarアカウントを持っているならアカウントメールアドレス  

<i class="fas fa-chevron-circle-right"></i> follow_button  
フォローボタンのリンク先（任意）  

<i class="fas fa-chevron-circle-right"></i> social  
各種SNSサービス等のアカウント情報（任意）  

## Netlify との連携
Netlifyは静的なサイトを超高速で提供できる無料のWebホスティングサービス  
静的コンテンツのWebホスティングだけでなく組み込みアプリケーションビルド機能を使うことで、Hugo などのサイトジェネレータも動かしてWebサイト構築が行える  
<i class="fas fa-external-link-alt"></i> [Netlify](https://www.netlify.com/)  

### Githubリポジトリの作成  
まず Github 上にアカウントを作成しリポジトリを作成します。  
その後ローカルに作成した Hugo プロジェクトを Github に Push します  

`<site name>` を Git プロジェクト化  

```
> C:\Users\username\Documents\Project\hugo\sites\<site name>
> git init .
```

カスタムテーマ clone 時の `.git` ファイルは不要であれば削除する  

```
C:\Users\username\Documents\Project\hugo\sites\<site name>\themes\.git
```

初期段階でもいのでコミットし
GitHub 上に Push  

```
> git add .
> git commit .
> git remote add origin <github URL>
> git push origin master
```

### Netlifyの登録  
キャプチャ取り忘れたけどそれほど難しくない  
まずサイトにアクセスして 「**Sign Up→**」  
<br />
<img src="/images/2019/create-hugo/image01.png" />  

アカウント登録後、再度ログインして管理画面にきたら　「**New site from Git**」 を選択  
<br />
<img src="/images/2019/create-hugo/image02.png" />  

連携リポジトリ先を聞かれるので先程作成した Github リポジトリを選択  
すべてのリポジトリに対して Netlify から権限許可を与えますか？がデフォルトになってるが、公開したいリポジトリのみを選択する  
<br />
<img src="/images/2019/create-hugo/image03.png" />  

デプロイ内容の確認画面  
ビルドコマンドに `hugo` が選択されている事を確認  
<br />
<img src="/images/2019/create-hugo/image04.png" />  

設定完了すると初回時は自動でデプロイ処理が走る  
通常であれば問題ないが、カスタムテーマの関係で実行フォルダが変わっているため自分の場合デプロイは失敗した  
管理画面から -> 「Deploys」 -> 「Deploy settings」より **Base directory** を指定する  
<br />
<img src="/images/2019/create-hugo/image05.png" />  

再度デプロイして成功していることを確認  
<br />
<img src="/images/2019/create-hugo/image06.png" />  

サイトドメインを事前に設定していた値に登録  
管理画面から -> 「**Settings**」  
<br />
<img src="/images/2019/create-hugo/image07.png" />  

希望のドメインが空いている事を祈りましょう  
これでサイトを確認しローカルと同じく表示されていることを確認できれば完了です  

## テンプレートのカスタマイズ  

### 更新日時も表示されるように設定  
Git のコミット情報から自動で更新日時を取得するように設定  

```
> config.toml
enableGitInfo = true
```

取得した値を表示するよう組み込み  

```
> layouts/partials/article_header.html
# 10行目付近  
        <div class="article-date">
            <i class="fa fa-calendar"></i>
            <time datetime="{{ .Date }}" itemprop="datePublished">{{ .Date.Format .Site.Params.date_format }}</time>
            &middot;

            <!-- 更新日時設定 -->
            <i class="fa fa-repeat"></i>
            <time datetime="{{ .Lastmod }}" itemprop="datePublished">{{ .Lastmod.Format .Site.Params.date_format }}</time>

            <!--{{ .WordCount }}
            {{ with .Site.Data.l10n.articles.words }}{{.}}{{end}} -->
            &middot;
            <i class="fas fa-clock"></i>
            {{ .ReadingTime }}
            {{ with .Site.Data.l10n.articles.readingtime }}{{.}}{{end}}
        </div>
```

他にも記事の Predefined で手動指定もできる（らしい）  
<i class="fas fa-external-link-alt"></i> [[Front Matter Variables](https://gohugo.io/content-management/front-matter/#predefined)  

また Netlify の時間もタイムゾーン設定を行っておく  
<br />
<img src="/images/2019/create-hugo/image08.png" />  

### CodeHighlighting
Markdown でのコード記述を SyntaxHighlighting するように設定  
<i class="fas fa-external-link-alt"></i> [Syntax Highlighting](https://gohugo.io/content-management/syntax-highlighting/)  

```
> config.toml
pygmentsUseClasses = true
pygmentsCodefences = true
```

CSSを出力して配置（必要？）  

```
> hugo gen chromastyles --style=monokai > static\css\monokai.css
```

pygments を使うために Python もインストール  
<i class="fas fa-external-link-alt"></i> [Download the latest version for Windows](https://www.python.org/downloads/)  

```
> pip install Pygments
```

出力した CSS を読むこむように テンプレートHTML に追記  

```
    <link rel="stylesheet" href="{{ "css/monokai.css" | absURL }}">
```

### URLパーマリンクの変更  
利用しているカスタムテーマののパマリンクは自動で `/yyyy/mm/dd/title` となっていたため、  
日本語文字のタイトル記事を書くとURLがエンコードされてしまい良くないのでフォーマットを変更する  
<i class="fas fa-external-link-alt"></i> [URL Management](https://gohugo.io/content-management/urls/#permalinks)  

```
> config.toml
[permalinks]
    post = "/:year/:month/:day/:filename"
```

### カスタムCSS
カスタムCSSの有効化  
利用しているテーマでは下記のように指定してできた  

```
> config.toml
[params]
    # Add custom assets with their paths relative to the static folder
	custom_css = ["css/custom.css"]
```

CSSの配置先は  `exampleSite\static\css\` の方  
フォントの指定や テーマで採用されている FontAwsome が4系と古かったため５系に上書きもしている  

```
@import url(https://fonts.googleapis.com/earlyaccess/notosansjp.css);
@import url(https://fonts.googleapis.com/css?family=Open+Sans:600,800);
@import url(https://use.fontawesome.com/releases/v5.X.X/css/all.css);

body,h1,h2,h3,ul,p,li {
    font: 14px Roboto,"Noto Sans CJK JP","Noto Sans JP",Arial,Helvetica,sans-serif;
}
```

### FontAwsome 5アップデート対応  
ゴリゴリテンプレートのHTMLファイルを修正  
ついでにサイドバーにもアイコンを埋め込み  

```
> layouts\social.html
> layouts\profile.html
> layouts\article_footer.html
> layouts\article_header.html
> layouts\widgets\categories.html
> layouts\widgets\recent_articles.html
> layouts\widgets\tags.html
```
<br />
<iframe src="https://rcm-fe.amazon-adsystem.com/e/cm?o=9&p=12&l=ez&f=ifr&linkID=e3cbc3b4da7c4fd3d2f2cd82c15d695a&t=sinokyoufu-22&tracking_id=sinokyoufu-22" width="300" height="250" scrolling="no" border="0" marginwidth="0" style="border:none;" frameborder="0">
</iframe>
