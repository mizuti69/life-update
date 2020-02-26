+++
title = "AWS Firewall Manager でセキュリティグループが管理できる"
tags = ["aws"]
categories = ["SE"]
date = "2019-10-18"
description = "AWS Firewall Manager で VPCセキュリティグループ が管理できるようになったらしい"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## 更新内容  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/about-aws/whats-new/2019/10/aws-firewall-manager-now-supports-management-of-amazon-vpc-security-groups/" data-iframely-url="//cdn.iframe.ly/m8geEKx?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

今までは WAF と Shild と Config の管理だけだったのですが、やっと名に恥じない機能が追加されたらしい  
(いつのまに東京リージョンに対応してたんだ・・・・)  

## Firewall Manager  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/firewall-manager/" data-iframely-url="//cdn.iframe.ly/kAVNERM?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

~~Firewall Manager を利用する金額は発生せず、管理対象の WAF、Shild、Config 等の料金のみ~~  
~~料金ページにはまだ セキュリティグループについては特に書いてないけど セキュリティグループ自体には金額かからないので同じでしょう~~  
<i class="fas fa-external-link-alt"></i> [AWS Firewall Manager の料金](https://aws.amazon.com/jp/firewall-manager/pricing/)  

料金はポリシー数、ポリシーの適応アカウント数等で発生します  
例えばセキュリティグループのみを自分のアカウント内の管理だけで利用でも月額 100USD/ポリシー/リージョン で発生します（エグい）  

## 事前作業
<i class="fas fa-external-link-alt"></i> [AWS Firewall Manager の前提条件](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/fms-prereq.html)  
Firewall Manager はぞれ単体で提供されているサービスではなく、「WAF & Shild」の一部サービス的な感じで提供されているので  
「WAF & Shild」のサービスページにアクセスするとサイドメニューの下のほうにある  

<img src="/images/2019/aws-fwm/fwm-01.png" />  

### IAM 権限の付与  
最初アクセスすると下記のようなメッセージが出て警告された  

<img src="/images/2019/aws-fwm/fwm-02.png" />  

> Identity and Access Management (IAM) policies currently restrict your access to the console. Contact your AWS administrator if you need help. If you are an AWS administrator, see the AWS WAF documentation.
>
>Account: **************** is not currently delegated by AWS FM.

`AWS FM` にアクセスする IAM 権限が無いとさ  

なのでIAMに権限を追加しておく  

<img src="/images/2019/aws-fwm/fwm-03.png" />  

検証なのでとりあえず`FullAccess`をつけておく  
注意のメッセージも出ている  

> Your AWS account must be in an organization. Learn more
>
> Your AWS account must be in an organization in AWS Organizations, and the organization must have all features enabled (not just consolidated billing).

AWSアカウントを束ねて統合管理できるのがメリットのサービスだから組織を作っておけよと  

> You must designate an account as the AWS Firewall Manager administrator account.

さらに  
操作する AWSアカウントを AWS FM の管理者アカウントに登録しろよと  

### AWS Organizationsに参加する
<i class="fas fa-external-link-alt"></i> [AWS Organizations に参加する](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/join-aws-orgs.html)  
組織を作成し  

<img src="/images/2019/aws-fwm/fwm-04.png" />  
<img src="/images/2019/aws-fwm/fwm-05.png" />  
<img src="/images/2019/aws-fwm/fwm-06.png" />  

AWSルートアカウントに承認のメールが飛ぶためマスターアカウントとして承認し組織を作成する  

### FWM 管理アカウントの設定
FirewallManager にて 組織のAWSアカウントを FirewakkManager 管理用アカウントを登録する  

<img src="/images/2019/aws-fwm/fwm-11.png" />  
<img src="/images/2019/aws-fwm/fwm-12.png" />  
<img src="/images/2019/aws-fwm/fwm-13.png" />  

> Are you sure you want to proceed?
> 
> The AWS Firewall Manager account cannot be changed by this account once it is set. To set a different administrator, the current administrator must go to AWS Firewall Manager settings and remove their account as the administrator. Then the AWS Organizations master account can set a new administrator.  

一回登録すると変更できないよ  
変更するには登録した管理アカウントでコンソールから権限を削除して、AWS Organizations マスターアカウントで再設定する必要があるよとのこと  

### AWS Config の有効化  
FirewallManager は AWS Config を利用してルールの適応状況やルールポリシーの管理・内容を管理します  
事前に AWS Config を Firewall Manager で利用する AWS Organizations のメンバー全ての環境で有効化する必要があります、セットアップしておきましょう  
AWS Config の有効化はここでは割愛します  

### ルールの作成
ここからは FireWall Manager の管理アカウントに設定した AWSアカウントで作業を行います  
**管理作業は登録した管理ユーザーでしかできません**  

ここでいうポリシーとはサービスに適用するルールではなく、FirewallManager上での管理ポリシーです  

<img src="/images/2019/aws-fwm/fwm-14.png" />  

セキュリティグループを選択し、既存のセキュリティグループ管理を選択  

<img src="/images/2019/aws-fwm/fwm-15.png" />  

ポリシー名の入力  

<img src="/images/2019/aws-fwm/fwm-16.png" />  

ポリシー管理対象のセキュリティグループを選択  
デフォルトでは１つのポリシーに対して１つのセキュリティグループのみ選択できます  
ここでは EC2 に必ず指定するデフォルト的なセキュリティグループを対象にします  
※２個選択すると最後に怒られる  

<img src="/images/2019/aws-fwm/fwm-17.png" />  

<img src="/images/2019/aws-fwm/fwm-18.png" />  

選択したセキュリティグループに対する管理アクションを選択します  
ポリシールールに準拠していない自動修正しないリソースの特定か、  
ポリシールールを適用し準拠していないリソースを自動修正するか  

<img src="/images/2019/aws-fwm/fwm-19.png" />  

ルールポリシーの適応範囲を指定  
どのAWSアカウントに対して指定するか、それとも全てか？  
リソースタイプはどれか？  
全てのリソースタイプに対してか？タグ指定か？
を選べます  

<img src="/images/2019/aws-fwm/fwm-20.png" />  

最後に確認  
ちなみに管理対象のセキュリティグループは全て IP ベースでポリシーを管理している必要があるらしく、  
セキュリティグループ定義内に「他セキュリティグループIDからの通信は許可」みたいなセキュリティグループIDの定義があると怒られます  

問題なければ以下のように  
「AWS Organizations の組織メンバーに対して AWS Config を有効化しましたか？AWS Configを有効にする必要があることを理解し、それに関連する料金を支払うことに同意します」  
に同意を求められます  

> Important:
> You must enable AWS Config for each member account in your AWS Organization and for each AWS Region that contains the resources that you want to protect. You can enable AWS Config manually, or you can use the AWS CloudFormation template "Enable AWS Config" at AWS CloudFormation StackSets Sample Templates.
> 
> Important note about charges: You will incur charges for your AWS Config settings according to
> AWS Config pricing.
> 
> I understand that I have to enable AWS Config, and agree to pay the charges associated with it.

<img src="/images/2019/aws-fwm/fwm-21.png" />  

同意し作成完了  
自動復旧ルールが設定してないから警告が出てる  

<img src="/images/2019/aws-fwm/fwm-22.png" />  

---

使ってみてなんとなく出来ること、出来なさそうなことがわかってきました  
基本的には 同一組織、あるいはプロジェクト内における複数 AWS アカウント環境への一元管理が目的ということだね  

<i class="fas fa-chevron-circle-right"></i> イメージとしては複数AWSアカウントを同一 Config ルールで管理するツール  
<i class="fas fa-chevron-circle-right"></i> AWSアカウントをまたいだ AWS Config ルールの適用、管理  
<i class="fas fa-chevron-circle-right"></i> AWSアカウントをまたいだ WAF、Shikd、セキュリティグループの作成  
<i class="fas fa-chevron-circle-right"></i> FirewallManagerでポリシーを作成した場合は、複数AWSアカウントに対して適応できる  
<i class="fas fa-chevron-circle-right"></i> セキュリティグループなど、既存のポリシーを管理する場合はコンプライアンスや変更などの監査管理のみ  
<i class="fas fa-chevron-circle-right"></i> 全体用の基本的なテンプレートルールの作成、管理であり、個別環境設定、ルールの管理には適さない（出来るけど量が煩雑になる）  

単一アカウントでもセキュリティグループの管理に使えるかなーって飛びついたけど  
どうやらそういう物ではなさそうだし、オーバースペックのようだ AWS Config だけで十分  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392568/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61N%2BYkPAOtL._SL160_.jpg" alt="Amazon Web Services 業務システム設計・移行ガイド (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
