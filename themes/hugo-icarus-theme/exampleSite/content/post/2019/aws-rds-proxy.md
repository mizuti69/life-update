+++
title = "AWS WAF AwsManagedRules 検証"
tags = ["aws"]
categories = ["SE"]
date = "2019-12-18"
description = "AWS WAF に 無料の AWS Managed Rule が追加されたようなのでどうやって使うか確認してみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS WAF
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/waf/" data-iframely-url="//cdn.iframe.ly/Ul1BkQA?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

### 料金
* Web ACL	5.00USD/月あたり (時間で案分)
* ルール	1.00USD/月あたり (時間で案分)
* リクエスト	0.60USD/100 万リクエストあたり

プラス  

* マネージドルールの利用料金  

マネージドルールの料金は [Marketplace](https://aws.amazon.com/marketplace) とかで探してみると金額がわかる  

<i class="fas fa-external-link-alt"></i> [Fortinet Managed Rules for AWS WAF - Complete OWASP Top 10](https://aws.amazon.com/marketplace/pp/B081SK32C7)  
<img src="/images/2019/aws-waf/amr-01.png" />  

設定されているルール数とそれらを処理したリクエスト数に応じて料金が発生する  
AWS WAF を紐付ける CloudFront や ALB 、 API Gateway の料金は別途発生する  

さらに知っておくべきこととして  

* AWS Managed Rules の使用に関しての追加料金などはありません  
* AWS Marketplace の販売者が提供するマネージド型ルールにサブスクライブすると、販売者が設定したマネージド型ルール料金がかかります  
* AWS WAF の料金体系には変更はありません  

## WAF の作成
<i class="fas fa-external-link-alt"></i> [AWS WAF 用 AWS Managed Rules の発表](https://aws.amazon.com/jp/blogs/news/announcing-aws-managed-rules-for-aws-waf/)  
<i class="fas fa-external-link-alt"></i> [AWS WAF、AWS Shield、AWS Firewall Manager とは](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/what-is-aws-waf.html)  

だいぶ以前はWAFを設定したいけどマネージドルールはなんか評価低いのばかりでお金に見合う対価があるのか微妙で、  
AWSの標準WAF ACLs を CloudFormation から導入して格安に運用していたんだけど  
ついにAWSのマネージドルールが出たらしく実際に設定してみる  

### WAF ACLsの作成
<img src="/images/2019/aws-waf/amr-02.png" />  
<img src="/images/2019/aws-waf/amr-03.png" />  

ルールを作成する際に「Add managed rule groups」 を選択する  

<img src="/images/2019/aws-waf/amr-04.png" />  

マネージドルールに「AWS managed rule groups」ができてる  

<img src="/images/2019/aws-waf/amr-05.png" />  

<i class="fas fa-chevron-circle-right"></i> Admin protection
公開された管理ページへの外部アクセスをブロックできるルール  

> Contains rules that allow you to block external access to exposed admin pages. This may be useful if you are running third-party software or would like to reduce the risk of a malicious actor gaining administrative access to your application.  

<i class="fas fa-chevron-circle-right"></i> Amazon IP reputation list  
ボットやその他のAmazon脅威インテリジェンスに基づくソースをブロックするルール  

> This group contains rules that are based on Amazon threat intelligence. This is useful if you would like to block sources associated with bots or other threats.  

<i class="fas fa-chevron-circle-right"></i> Core rule set  
OWASPの出版物や一般的なCommon Vulnerabilities and Exposures（CVE）に記載されているものを含む、広範な脆弱性の悪用に対する保護が提供  

> Contains rules that are generally applicable to web applications. This provides protection against exploitation of a wide range of vulnerabilities, including those described in OWASP publications and common Common Vulnerabilities and Exposures (CVE).  

<i class="fas fa-chevron-circle-right"></i> Known bad inputs  
脆弱性の悪用または発見に関連付けられている要求パターンをブロックできるルール  

> Contains rules that allow you to block request patterns that are known to be invalid and are associated with exploitation or discovery of vulnerabilities. This can help reduce the risk of a malicious actor discovering a vulnerable application.  

<i class="fas fa-chevron-circle-right"></i> Linux operating system  
LFI攻撃など、Linux固有の脆弱性の悪用に関連する要求パターンをブロックするルール  

> Contains rules that block request patterns associated with exploitation of vulnerabilities specific to Linux, including LFI attacks. This can help prevent attacks that expose file contents or execute code for which the attacker should not have had access.  

<i class="fas fa-chevron-circle-right"></i> PHP application  
安全でないPHP関数の挿入など、PHPの使用に固有の脆弱性の悪用に関連する要求パターンをブロックするルール  

> Contains rules that block request patterns associated with exploiting vulnerabilities specific to the use of the PHP, including injection of unsafe PHP functions. This can help prevent exploits that allow an attacker to remotely execute code or commands.  

<i class="fas fa-chevron-circle-right"></i> POSIX operating system  
LFI攻撃を含むPOSIX / POSIXライクなOSに固有の脆弱性の悪用に関連するリクエストパターンをブロックするルール  

> Contains rules that block request patterns associated with exploiting vulnerabilities specific to POSIX/POSIX-like OS, including LFI attacks. This can help prevent attacks that expose file contents or execute code for which access should not been allowed.  

<i class="fas fa-chevron-circle-right"></i> SQL database  
SQLインジェクション攻撃など、SQLデータベースの悪用に関連する要求パターンをブロックできるルール  

> Contains rules that allow you to block request patterns associated with exploitation of SQL databases, like SQL injection attacks. This can help prevent remote injection of unauthorized queries.  

<i class="fas fa-chevron-circle-right"></i> Windows operating system  
Windows固有の脆弱性（PowerShellコマンドなど）の悪用に関連する要求パターンをブロックするルール  

> Contains rules that block request patterns associated with exploiting vulnerabilities specific to Windows, (e.g., PowerShell commands). This can help prevent exploits that allow attacker to run unauthorized commands or execute malicious code.  

<i class="fas fa-chevron-circle-right"></i> WordPress application  
WordPressサイト固有の脆弱性の悪用に関連するリクエストパターンをブロックするルール  

> The WordPress Applications group contains rules that block request patterns associated with the exploitation of vulnerabilities specific to WordPress sites.  


これいれておけばオッケー！みたいなルールか他マネージドルールみたいに数個かと思ったらわりと細分化されていた  
一般的なWebシステムに対してマネージドルールを入れようと思った場合、

* Amazon IP reputation list  
* Core rule set  
* Known bad inputs  
* SQL database  

これらをベースに組み合わせやシステム要件を見ながら調整するかんじかな  
有効化する際に「Set rules action to count」も有効化するとブロックはせずカウントのみとなる  

<img src="/images/2019/aws-waf/amr-06.png" />  

マネージドルールを選択してルールリストを見てみると  

<img src="/images/2019/aws-waf/amr-07.png" />  

**Capacity** に基づいて **Web ACL rule capacity units used** が減っているのがわかる  

だいぶ以前は  

>  [WAF] - [ACLs] - [Rules] 

と紐付いていてACLs あたり Rules は 10 までという上限があったのですが、  
WAF Capacity Unit (WCU) という考え方のルールに新しく変わり  

> [WAF] - [ACLs] - [WCU]
> The total capacity units used by the web ACL can't exceed 1500.  

という考え方に変わり、 ACLs に設定出来る WCU 最大 1500 以内であればルールを追加管理できるようになった  
マネージドルールの場合「Capacity」のところに消費する WCU が記載されている  
マネージドじゃない場合の手動でルールを定義する場合は１ルール１WCUなのか？  

まぁそれらは置いておいて次に進むとルールの Priority が設定できる  

<img src="/images/2019/aws-waf/amr-08.png" />  

上から順番に処理されるためネットワーク層に対するWAFポリシー系を上げておく  

CloudWatch メトリクスの指定、そのまま  

<img src="/images/2019/aws-waf/amr-09.png" />  

以上で作成完了  

<img src="/images/2019/aws-waf/amr-10.png" />  

### AWS リソースとの関連付け
今回は ALB 向けに作っていたので 既存の ALB に紐付ける  

<img src="/images/2019/aws-waf/amr-11.png" />  
<img src="/images/2019/aws-waf/amr-12.png" />  

ALB側から見ても関連付けされて無いように見えるが、ちゃんと有効になっており  
例えばデフォルトアクションを「Block」にすればちゃんと 403 画面が表示される  

<img src="/images/2019/aws-waf/amr-13.png" />  

### テスト
試しに ShellShock 時の脆弱性攻撃に似せた UserAgent 設定にしてアタックしても防がれなかった  

```
user-agent: () { :;}; echo something>/var/www/html/new_file
```

Scutum とか Akamai とかは防いでくれるけど、まぁ無料だとこんなもんなのかな？  

某ブログで確認してた URL リクエストを投げてみてもブロックされず  

```
$ curl -X GET -d '? or 1=1-- ' https://hogehoge.com/index.html
```

メトリクスとかで見たいのに確認出来ない  
少し運用しながら確認してみる  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797393165/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/513hw-04L8L._SL160_.jpg" alt="体系的に学ぶ 安全なWebアプリケーションの作り方 第2版 脆弱性が生まれる原理と対策の実践" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
