+++
title = "AWSのセキュリティ対策を読んで"
tags = ["aws"]
categories = ["SE"]
date = "2019-11-07"
description = "「AWSでのセキュリティ対策全部盛り【初級から中級まで】」のスライド資料を読んでのメモ"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## 参考資料
<div style="left: 0; width: 100%; height: 0; position: relative; padding-bottom: 56.1972%;"><iframe src="https://speakerdeck.com/player/aba22209644646ee9ff21ef72d5a439d" style="border: 0; top: 0; left: 0; width: 100%; height: 100%; position: absolute;" allowfullscreen scrolling="no" allow="encrypted-media"></iframe></div>  

### CoE という考え方
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://base.terrasky.co.jp/articles/B8z3Y" data-iframely-url="//cdn.iframe.ly/XPuAlB7?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

> COEはCenter Of Excellenceの略です。この言葉の由来は1940～50年代にまで遡り、米国カリフォルニア州のスタンフォード大学で当時多くの優秀な学生が東海岸へ流出してしまうことを防ぐため、全米から著名な教授・スタッフを呼寄せ、学究拠点の中心とする施策から来ているようです。その後、様々な大学や研究機関での施策・拠点として発展、企業においても優秀・有識なリソースを集約し組織運営を向上化・効率化するという形で適用され、ITの分野でも使われるようになったと理解しています。要は、会社の中でシステム化を進めていく際に多くのプロジェクト、タスクが走ると思いますが、その中で専門性を持った人材を組織横断的に配置し、ベストプラクティスを集約、適用、運用することにより、成功裡、効率的にシステム導入していくための仕組み・組織  

### NIST
<i class="fas fa-external-link-alt"></i> [IPA - セキュリティ関連NIST文書](https://www.ipa.go.jp/security/publications/nist/)  

> NIST（National Institute of Standards and Technology：米国国立標準技術研究所）は、科学技術分野における計測と標準に関する研究を行う米国商務省に属する政府機関です。  
> NIST内には、情報技術に関する研究を行っているITL（Information Technology Laboratory）があります。  
> ITLは情報技術に関して6つの分野（Security, Information Access, Mathematics and Computational Science, Software Testing, Networking Research, Statistical Engineering）の研究を行っており、ITLの中でコンピュータセキュリティに関して研究を行い各種文書を発行しているのがCSD（Computer Security Division）と呼ばれる部門です。FIPSやSP800シリーズの文書も、CSDが発行しています。  

その中で以下のような指標文書群があり  

<i class="fas fa-chevron-circle-right"></i> FIPS  
推奨する管理策や要求事項、暗号化やハッシュ化、認証、デジタル署名およびLANのセキュリティなど、分野別に、詳細な基準や要求事項、ガイドラインを示し、政府機関のみならず、民間企業にとっても、情報セキュリティ対策を考える上で有用な文書  

<i class="fas fa-chevron-circle-right"></i> SP800  
SP800シリーズは、CSDが発行するコンピュータセキュリティ関係のレポートです。米国の政府機関がセキュリティ対策を実施する際に利用することを前提としてまとめられた文書ですが、 内容的には、セキュリティマネジメント、リスクマネジメント、セキュリティ技術、セキュリティの対策状況を評価する指標、セキュリティ教育、インシデント対応など、セキュリティに関し、幅広く網羅しており、政府機関、民間企業を問わず、セキュリティ担当者にとって有益な文書  

AWSは 「NIST 800-53 Revision 4」 統制と追加の FedRAMP 要件に関して第三者が実施するテストによって検証されている  
<i class="fas fa-external-link-alt"></i> [AWS 米国国立標準技術研究所 (NIST)](https://aws.amazon.com/jp/compliance/nist/)  

<i class="fas fa-chevron-circle-right"></i> **NIST 800-53 Revision 4**  
**「連邦政府情報システムおよび連邦組織のためのセキュリティ管理策とプライバシー管理策」**

IPA のサイトよりPDFを確認してみると500ページ以上あるため要約と目次だけ目を通してみる  
内容はセキュリティ管理思考指標と付録ではセキュリティ・リスク管理用のチェックリストがついている  

AWSもこの指標を満たせるよう責任共有モデルに基づいてサービスを構成・提供し、  
ユーザーが求める場合にはそれに対応できるよう準備しているよと  

> セキュリティ許可の目的で、(NIST 800-53 rev 4 低/中/高の統制ベースラインに基づく) FedRAMP 要件へのコンプライアンスは、AWS が AWS のみと共有統制を完全に実施することと、お客様がお客様のみと共有統制を実施することが条件となります。AWS の責任である統制が AWS に実装されていることは、FedRAMP 公認の第三者評価機関 (3PAO) によって評価され、認証されています。お客様に責任のある共有統制の一部と、お客様が AWS インフラストラクチャの上に実装しているアプリケーションに関連する統制は、NIST 800-37 と、お客様特有のセキュリティ許可ポリシーおよび手順に合意のうえで、お客様が個別に評価し、承認する必要があります。  

### AWS セキュリティベストプラクティス
<i class="fas fa-external-link-alt"></i> [AWS セキュリティのベストプラクティス](https://d1.awsstatic.com/whitepapers/ja_JP/Security/AWS_Security_Best_Practices.pdf)  
毎度おなじみ  

### AWS Well-Architected ホワイトペーパー
<i class="fas fa-external-link-alt"></i> [AWS Well-Architected フレームワークホワイトペーパー](https://aws.amazon.com/jp/blogs/news/aws-well-architected-whitepaper/)  
AWSのナレッジを勉強しようてきな  

### AWS 各種コンプライアンス対応
<i class="fas fa-external-link-alt"></i> [AWS コンプライアンスプログラム](https://aws.amazon.com/jp/compliance/programs/)  
知識として  

## 初心者～中級者として学習したほうが良いもの  

### IAM のベストプラクティス  
<i class="fas fa-external-link-alt"></i> [IAM のベストプラクティス](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/best-practices.html)  
S3 のアクセスポリシーを触ったときも思いましたが、IAM、ロールの権限は本当に強い  
つい目的のために横着していしまう部分なのでベストプラクティスに従い理解を深めておく  
よくみる AWS の個人情報流出はこの辺が突破されている気がする  

### AWS ご利用開始時に
<i class="fas fa-external-link-alt"></i> [AWS ご利用開始時に最低限おさえておきたい10のこと](https://pages.awscloud.com/event_JAPAN_at-least-10-ondemand.html?trk=aws_event_page)  

一度 Well-Architected Framework に触れておきましょうというお話  
サービスとして利用する際、AWSが問いかける内容にちゃんと答えられますか？と確認、認識、反省、行動とサイクルを回すことが出来る  

### AWS CloudTrail
AWS内部のAPIコールを全て記録する監査ログ  
1つの監査ログを取得するだけならそれほどお金もかからず、また何か問題があったときに無いと調査も出来ないため必須  

### AWS Config
AWSリソースの変更を監視、検知、修復  
意図しないAWSリソースの変更を改ざん検知間隔で監視できるためサービス環境では必須  

### Amazon GuardDuty
VPCのトラフィックログや通信を監視して不正なアクセスや操作、通信を監視する  
ブラックボックスになりがちなAWS VPC内部の通信や外部からの怪しいアクセスを監視検知してくれるため必須  

### AWS Shild  
AWS では無料で L3、L4 に対する攻撃が保護されている  
知識として知っておこう  

### AWS Trusted Advisor
上記にあげて来きたようなベストプラクティスの内容を元に環境を自動で精査し改善点を指摘してくれる  
基本的には無料  
その他「コストパフォーマンス」や「パフォーマンス」「サービス制限に近いもの」など色々精査してくれる  

### ネットワーク構造
三層ネットワーク構造を心がける  
パブリッククラウドでは基本構成になってきていると思うDMZにモノ置くなという感じ  

Azureのドキュメントがわかりやすいかも  
<i class="fas fa-external-link-alt"></i> [n 層アーキテクチャのスタイル](https://docs.microsoft.com/ja-jp/azure/architecture/guide/architecture-styles/n-tier)  

メリット・デメリットがあるため自分が作りたいサービスが適しているのか、改善・妥協する部分はどこなのか考慮しておくことが大事  

### S3 のセキュリティベストプラクティス  
<i class="fas fa-external-link-alt"></i> [Amazon S3 のセキュリティのベストプラクティス](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/security-best-practices.html)  

・Amazon S3 バケットに正しいポリシーが使用され、それらのバケットが公開されていないことを確認する  
・最小限の特権アクセスを実装する  
・Amazon S3 アクセスを必要とするアプリケーションと AWS サービスには IAM ロールを使用する  
・Amazon S3 アクセス用の VPC エンドポイントを検討する  

そのほかモニタリングの方法も教えてくれる  

## CIS AWS Benchmark
<i class="fas fa-external-link-alt"></i> [CIS AWS Foundations Benchmark](https://www.cisecurity.org/benchmark/amazon_web_services/)  

> CIS AWS Foundations Benchmark は、AWS に関する一連のセキュリティ設定のベスト プラクティスです。これらの業界で認められているベスト プラクティスは、これまでの高度なセキュリティガイダンスよりも優れ、AWS ユーザに明確で段階的な実装および評価手順を提供します。  

もうどんだけ AWS は世界中のセキュリティ会社から指標を提示されてんだよ  
この CIS に対応、準拠しようとルールを組み込みたい場合に利用出来るサービスが **AWS Security Hub**  
<i class="fas fa-external-link-alt"></i> [AWS - コンプライアンス標準: CIS AWS Foundations](https://docs.aws.amazon.com/ja_jp/securityhub/latest/userguide/securityhub-standards.html)  

本家の資料は英語のため読み切れていないけれど、
大量で煩雑な CloudTrail の何を監視したら良いのかについてのアドバイスもあり  

* AWS コンソールへのアクセス  
* S3 へのアクセス  
* CloudTrailへのアクセス  

なんかは当たり前に見ろよと言っている  

## AWS セキュリティブログ
<i class="fas fa-external-link-alt"></i> [AWS Security Blog](https://aws.amazon.com/jp/blogs/security/)  

AWSのブログは見ていたけど、セキュリティブログがあるのは初めて知った  
日本語ページは無いからそもそも日本語のブログサイト見てるとカテゴリリンクすら無いのね、迂闊  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4822237443/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/51KpOuWyhYL._SL160_.jpg" alt="Amazon Web Services 基礎からのネットワーク&サーバー構築 改訂版" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
