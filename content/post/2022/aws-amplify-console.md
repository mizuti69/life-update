+++
title = "安易に AWS Amplify Console を採用して後悔した話"
tags = ["aws"]
categories = ["SE"]
date = "2022-01-27"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

安易に AWS Amplify Console を採用して利用すると、構築は楽だけど運用は大変という話  

<!--more-->

## AWS Amplify Cosnsole について
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/amplify/" data-iframely-url="//iframely.net/uQllpCT?card=small"></a></div></div><script async src="//iframely.net/embed.js" charset="utf-8"></script>

AmplifyとはAWSの提供するアプリケーション環境を簡単に素早く構築できるようにするツールと一連の機能の総称の事。  
大きく分けて以下のようになっており  

* Amplify Backend  
  アプリケーションが動くための簡単なホスティング環境を提供するコア環境  
* Amplify Console  
  Amplify Backend にホスティングサービスとして必要な機能、リダイレクトやGit、Webhook機能、認証や管理画面などホスティング運用や管理に必要な機能を詰め込んでパッケージングされた環境を簡単につくれるサービス  
* Amplify CLI  
  Amplify Backend をコアに、各種必要なAWSサービスを1つのスタック、CloudFormationでCLIから管理構築出来るようにするツール  
* Amplify Studio  
  Amplify CLIとノーコードツールを組み合わせ、より簡単に、素早くアプリケーションと環境を構築できるようにしたパッケージサービス  

それぞれ特性ややりたいことに対して必要な組み合わせを選択していく事で柔軟にAWS環境を構築出来ることがメリットとしてある。  

## Amplify Console について
利用した Amplify Conesole は AWS のポータル画面から UI を操作して簡単に環境を構築していくことが出来る  
環境作成の前に事前に連携する Git リポジトリを作成しアプリを配置しておく必要がある。  

標準機能として  

* Gitリポジトリとの連携機能
* ドメイン管理、カスタムドメイン設定機能  
  * ACMの自動作成  
* ビルド設定画面、エディター  
  * Webhookの作成  
* プルリクエストのマージ変更前プレビュー機能  
* ビルドをAWS SNSで通知できる機能  
* ビルド時に利用できる環境変数定義機能  
* 簡易認証機能（アクセスコントロール）  
* モニタリング  
  * CloudWatchメトリクス表示  
  * CloudWatchアラームダッシュボード  
  * アクセスログ
* 簡易リダイレクト定義機能  
* 応答ヘッダーのカスタム機能・エディター  

上記のような機能が Backend と違い初期からセットアップさた状態で利用できるのが特徴  

## 後悔した話  
結論からするとサービスモデルに沿った利用をしないのであれば、そりゃ使いにくいし失敗するよねという単純な話  

Amplify Console カスタマイズがしづらい。Amplify Console で完結できない環境が必要なのであれば利用しないほうがいい  
Backendでイチからサービスを組み立てていったほうが結果的に運用は楽になっただろう  

### CloudFrontを組み合わせる場合の注意点

##### Hostヘッダー問題  
Amplify Console 自体バックエンドとしては CloudFront のコンテンツデリバリーネットワークを利用しているが、  
細かいキャッシュ制御やオリジン制御をできないので別途 CloudFront を Amplify の前段に持ってきがちなのだが  
その場合 Amplify 側で受け付ける Host ヘッダー情報が変わってしまうためヘッダーを意識した処理を行いたい場合には注意が必要  

(User) -> www.hogehoge.com -> CloudFront -> amplify.hogehoge.com -> Amplify

同様に www なしドメインを www 有りドメインにリダイレクトしたいとなっても Amplify のリダイレクト設定では処理できない  

Amplify Console ではなく、Amplify Backend であれば CloudFront と連携する機能があるため上記問題は起きない  

#### キャッシュポリシーの罠  
CloudFrontのキャッシュコントロールポリシーに Amplify とあり、さも Amplify の為のポリシーに思えるが  
Amplify Backend と連携するためのポリシーとなっており、キャッシュキーに Host ヘッダーが入っている  
Hostヘッダー問題で触れたように、Amplify Console と CloudFront を組み合わせた場合、それぞれに意識する Host 情報はズレてくる  
Amplify 自体はデフォルトのドメインも持っており、SSL通信も出来るようになっており Host ヘッダーを意識している  

ユーザーからの Host ヘッダーがそのまま Amplify に伝わってしまうと SSL証明書エラーにもなるし認証も通らなくなるしで通信できなくなるので注意  

#### キャッシュ制御  
Amplify Console には カスタムヘッダー機能があり、通常はこちらで特定 URL やコンテンツに対してヘッダーでキャッシュ制御を行う必要が出てくる  
CloudFront でするか Amplify 側でするか、余計な混乱や問題の切り分けを複雑化してくるのでちゃんと考える必要がある  

### ビルド処理の注意点  
amplify.yml（Codeシリーズだとbuidspec.yml）でビルドに関する定義、ビルド環境に利用するコンテナイメージの指定まではできるが  
同時実行数等に関する細かな制御や pipline のようにちょっとしたワークフローを組むこともできないため、更新頻度が低いアプリでの利用を意識しないと  
思わぬビルド事故を起こすこともありそう  
Webhookも同様なので外部サービスと連携するときは外部で制御する必要がある  

同様にAmplifyに設定されているロールもAWS上からは認識、カスタマイズできないためビルド処理と一緒にCloudFrontのキャッシュクリア処理を叩く、のような事もできない  

### 自動構築 ON/OFF の注意点
Git連携による自動でのビルド処理するかしないかを ON / OFF で制御できるが、 ON だったものを途中で OFF にしある程度コミットが進んでから再度 OFF にしたときに古いコミットでビルドがされてしまう問題  

```
Note: switching to '5a9ac562bf22008d8a29b57608d4caf5288e52c6'.
                                 You are in 'detached HEAD' state. You can look around, make experimental
                                 changes and commit them, and you can discard any commits you make in this
                                 state without impacting any branches by switching back to a branch.
                                 If you want to create a new branch to retain commits you create, you may
                                 do so (now or later) by using -c with the switch command. Example:
                                 git switch -c <new-branch-name>
                                 Or undo this operation with:
                                 git switch -
                                 Turn off this advice by setting config variable advice.detachedHead to false
                                 HEAD is now at 5a9ac56 Merge branch 'develop' into feature/styleguide_layouts
```

リポジトリ側でHEAD位置がずれてないか、調整して再デプロイが必要  

### アクセスログ
標準でアクセスログを取得できる条件は以下  

* カスタムドメインで設定したカスタムドメインへのアクセス
* ブランチサブドメインが付いていない Amplify ドメインに対するアクセス

サブドメインに対するアクセスについてはログ出力されないので注意  

## さいごに
最初に使い方が悪かった、と書いてしまっているが実際目的は Amplify Console で達成できていて、細かいところで色々困ったという話  
マネージドサービスを利用することによる自由度とコストはトレード・オフなので当然ではある  

一番後悔していることは「あれ？これAmpify Cossole」じゃなくてよくね？と結果的になってしまったPJに対する自分のちからのなさなんだろう  
