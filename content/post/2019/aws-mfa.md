+++
title = "ALB + Cognito 認証で 2要素認証を実現する"
tags = ["aws"]
categories = ["SE"]
date = "2019-08-30"
description = "ALB + Cognito 認証に SMS 認証を入れて2段階認証を行えるようにする"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="http://blog.serverworks.co.jp/tech/2018/07/25/alb-authentication-cognito/" data-iframely-url="//cdn.iframe.ly/5tnQ67k"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

上記参考にしたサイト  

## Cognitoのセットアップ  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/cognito/" data-iframely-url="//cdn.iframe.ly/uK3FQSl?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### ユーザープールの作成  
ALBで参照する cognito の ユーザープールを作成する  
<i class="fas fa-external-link-alt"></i> [ユーザープールリファレンス (AWS マネジメントコンソール)](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/cognito-user-pools-getting-started-step-through-settings.html)  

<img src="/images/2019/aws-cognito/cognito_alb01.png" />  
<img src="/images/2019/aws-cognito/cognito_alb02.png" />  
<img src="/images/2019/aws-cognito/cognito_alb03.png" />  

初めてなので「ステップに従って設定する」を選択  

<img src="/images/2019/aws-cognito/cognito_alb04.png" />  

Eメールアドレスでログインできるようにする  

<img src="/images/2019/aws-cognito/cognito_alb05.png" />  

ログイン必須要素も同様  
後で変更出来ないため注意し、よくわからない場合はトライアンドエラーをちゃんと行っておかないと  

<img src="/images/2019/aws-cognito/cognito_alb06.png" />  

カスタム属性はスキップ  

<img src="/images/2019/aws-cognito/cognito_alb07.png" />  

パスワード強度  
デフォルトでは 7日間 アクティブでない場合失効してしまうため要件に応じて調整が必要そう  

<img src="/images/2019/aws-cognito/cognito_alb08.png" />  

ユーザープール全体で MFA を有効化出来るかどうかは初期作成時のここでしか設定出来ない  
MFA を使うかどうかはユーザー作成時に設定できるため、基本は有効化しておいたほうが無難そう  

MFA で SMS をチェックした場合表示のように 
**重要: ユーザーに SMS メッセージを送信して電話番号の確認や MFA を行うには、Amazon SNS に対して使用量の上限の引き上げをリクエストする必要があります。**とでてくる  
これは Amazon SNS で送る SMS のデフォルト上限利用金額が 最大 1$ になっているため  
利用するなら 上限金額をあげておけよということらしい⇩  

<img src="/images/2019/aws-cognito/cognito_alb09.png" />  

パスワード再発行時に利用するのは必須要素の Eメールにしておく  

<img src="/images/2019/aws-cognito/cognito_alb10.png" />  

Cognito の SMS キック用ロールを作成  

<img src="/images/2019/aws-cognito/cognito_alb11.png" />  

Eメールのカスタマイズ  
選べるリージョンは「バージニア」「オレゴン」「アイルランド」  
パスワード通知時に飛ぶメールなので特にカスタマイズしない  

デフォルトは下記のようなメールで飛んでくる  

> 件名：仮パスワード  
> From：no-reply@verificationemail.com  
> BODY：ユーザー名は hogehoge@mogemoge.com、仮パスワードは ####### です。

<img src="/images/2019/aws-cognito/cognito_alb12.png" />  

SESサービス経由でのメール送信を選べば From メールアドレスを指定できるらしい  
今回はしない  

ちなみにここでは出てこないが最後まで作成すると下記のようなメッセージが出てくる  

> 注意: Cognito がお客様の代わりに E メールを送信することを選択しました。
> ベストプラクティスは、毎日の E メールの制限から、本番ユーザープール用に Amazon SES を介してお客様が E メールを送信することです。

すでに SES を設定しているのであればそうした方がいいのかも知れない  

<img src="/images/2019/aws-cognito/cognito_alb13.png" />  
<img src="/images/2019/aws-cognito/cognito_alb14.png" />  

メッセージのカスタマイズもしない  

<img src="/images/2019/aws-cognito/cognito_alb15.png" />  

必要であれば  

<img src="/images/2019/aws-cognito/cognito_alb16.png" />  

ユーザーとアクセス端末情報をトラッキングし、マッチング記録してよりセキュリティを高められますよとのこと  
MFA を利用する場合端末指定で送ることもできるらしい  
今回はスキップ  

<img src="/images/2019/aws-cognito/cognito_alb17.png" />  

認証されたトークンを持つ端末からのみ Cognito の認証API をキックできるよう、端末を登録しますかとのこと  
今回は ALB で連携するので後で作成する  

<img src="/images/2019/aws-cognito/cognito_alb18.png" />  

認証フローの処理流に Lambda をキックしてより複雑な認証処理をカスタマイズできる  
しない  

最後に設定内容を確認し作成すると完了  
**作成後設定変更箇所がある**ためちゃんと確認しておく  

### クライアントアプリ設定
作成しただけではまだ使えない  
作成したユーザープールを選択し、以下を変更する  

<img src="/images/2019/aws-cognito/cognito_alb19.png" />  

後日談だが「時間ベースのワンタイムパスワード」にチェックを入れていると MFA が上手く動かず？  
SMS 認証してないのにログイン出来てしまうことがあったためチェックを外しておく  

<img src="/images/2019/aws-cognito/cognito_alb20.png" />  

ALB 用のアプリクライアントキーを作成  
これでこのユーザープールは ALB からしかキックされない  
名前は任意でいい  

<img src="/images/2019/aws-cognito/cognito_alb21.png" />  

クライアントアプリ、今回の場合でいうと ALB の認証用設定を登録する  
この辺は <i class="fas fa-external-link-alt"></i> [ALB + OIDC (Google認証)](/2019/08/23/aws-alb-oidc/) と同じ  

<img src="/images/2019/aws-cognito/cognito_alb22.png" />  

Cognito の ユーザープール専用ドメインを作成する  

#### ユーザーの作成
認証に使うユーザーを登録する  

<img src="/images/2019/aws-cognito/cognito_alb24.png" />  

ユーザー名はメールアドレスと同じにしておく  
電話番号は国際電話の記述に則って登録する必要がある  
作成すると作成時に設定したメールの内容で仮パスワードのメールが飛んでくる  
なお**ユーザー情報は作成後変更できない**ため間違えた場合は再作成する必要がある  

電話番号を記述すれば MFS を利用できるし、空欄にすれば利用できない  

## ALB の設定
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/elasticloadbalancing/" data-iframely-url="//cdn.iframe.ly/xAm1wTP?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

すでに作成済みの ALB に認証処理を追加  

### 認証設定
例えば特定の IP からアクセスがあった場合のみ認証処理をするようにルールを追加する  

<img src="/images/2019/aws-cognito/cognito_alb23.png" />  

認証処理で先程作成したユーザープール、アプリクライアントキーを選択する  

### 認証確認  
ブラウザでアクセスしてみる  

<img src="/images/2019/aws-cognito/cognito_alb25.png" />  

認証画面が出てきて、メールアドレス、仮パスワードを入力するとパスワードの初期化が求められる  

<img src="/images/2019/aws-cognito/cognito_alb26.png" />  

MFA 有効可していた場合ここで SMS に送られる認証用番号の入力が求められる  
SMSは遅いときは数分位かかって飛んでくることもあった  

2つの認証がクリアされるとサイトにアクセスすることが出来る  

1回ログインすれば同一セッションでれば認証は1回だけで済む  
こういう OpenAuth とかのセッション管理はどうなってるんだろ？Cookie？勉強しないとなぁ  

## ログイン状況の監査  
Cognito のユーザープールをつかってアカウント管理、認証管理を行った場合監査情報はどこかに保持されるのか？  

下記を読む限り、 Cognito に対する API コールや 設定変更については CloudTrail に残っていそうだが、
どのユーザーが、どの IP（端末）で 認証成功・失敗したかはわからないような気がする  
<i class="fas fa-external-link-alt"></i> [AWS CloudTrail を使用した Amazon Cognito API 呼び出しのログ作成](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/logging-using-cloudtrail.html)  

試しに CLoudWatchLogs で連携している CloudTrail のログを `cognito` とかで大雑把に検索してもそれらしいのは出てこなかった  
ここはもう少し調べる必要がありそう  

もし詳細に認証情報を取得したい場合は ユーザープールのトリガー機能を利用して、Lambda と連携して作り込めば取れるかも知れない  

またが 認証するユーザーが基本 MFA 認証を利用するのであれば、
Amazon SNS の SMS ログを CloudWatchLogs で連携すれば最低限誰がアクセスしたかはわかるか？  
※認証に成功すれば Webアプリケーション側にログが残るし、失敗すれば ALB のログに残って3つのログを突き合わせればなんとかなりそう...？  

### SMS のログ出力設定
Amazon SNS から SMS を選択し、配送ステータスのログオプションを設定する  
<i class="fas fa-external-link-alt"></i> [SMS 配信用の Amazon CloudWatch のメトリクスおよびログを表示する](https://docs.aws.amazon.com/ja_jp/sns/latest/dg/sms_stats_cloudwatch.html)  

<img src="/images/2019/aws-cognito/cognito_alb27.png" />  

> 「サンプルの成功率のデフォルト」 で、Amazon SNS が CloudWatch Logs でログに記録する正常な SMS 配信の割合を指定します。  
> たとえば、失敗した配信にのみログを書き込むには、この値を 0 に設定します。正常な配信の 10% に対してログを書き込むには、10 に設定します。  
> 割合を指定しないなら、Amazon SNS は、すべての正常配信に対してログを書き込みます。  

設定後、SNS を飛ばしてみると CloudWatch に下記のように出力されていた  

<img src="/images/2019/aws-cognito/cognito_alb28.png" />  
※デフォルトで保持期間が 30日 になっていたので監査目的なら変更しておこう  

```
{
    "notification": {
        "messageId": "**************************************",
        "timestamp": "2019-08-30 12:32:01.822"
    },
    "delivery": {
        "phoneCarrier": "SoftBank Mobile",
        "mnc": 20,
        "destination": "+8180********",
        "priceInUSD": 0.07148,
        "smsType": "Transactional",
        "mcc": 440,
        "providerResponse": "Message has been accepted by phone carrier",
        "dwellTimeMs": 351,
        "dwellTimeMsUntilDeviceAck": 3178
    },
    "status": "SUCCESS"
}
```

これならいつ、どの電話番号に SMS 認証コードを発行したかは確認できそうだ  
ついでに 1回あたりの金額もわかるのか  

あとは今回 ALB のロギングは有効化してなかったからわからないけど、  
Cognito の認証失敗した場合 ALB のログに出るのか、出るなら 401 エラーとかなのか？  
<i class="fas fa-external-link-alt"></i> [Application Load Balancer のアクセスログ](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/load-balancer-access-logs.html)  

マニュアルを見る限りは少なくとも認証に成功したか、失敗したかはログに出そうな気がする  
SMSのログと突き合わせれば いつ、どのアクセス元IPが、どの電話番号のユーザーで認証リクエストを出して、成功 OR 失敗 したかはわかりそう  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
