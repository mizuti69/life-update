+++
title = "AWS タグによる請求金額のグループ分けはどこまで出来る？"
tags = ["aws"]
categories = ["SE"]
date = "2020-05-12"
description = "AWS の請求用にタグで細分化をしようとした場合出来ないことはなんなのか把握しておく"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## コスト分配タグの利用
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/blogs/news/cost-allocation-tag/" data-iframely-url="//cdn.iframe.ly/3Mqgc2n?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

結論から言うと  
完全にタグに基づいた請求金額の完全な分配を望むのであれば AWS アカウントをプロジェクトや用途毎に新規で用意して  
AWS Organizations を使って管理しましょう  

### タグによるコスト分配することが出来るサービス  
<i class="fas fa-external-link-alt"></i> [Supported Resources](https://docs.aws.amazon.com/ARG/latest/userguide/supported-resources.html)  

タグによる請求を管理する場合、タグエディターによるタグ付け（通常のやり方）と  
Cost Categories にてカテゴリに関連付けさせるタググループと  
リソースグループにてタグ付けしたり管理するタグと  

アナログ管理、請求グループ管理、リソース管理グループ管理といくつか手法があり  
その手法によって分配することが出来るタグが違ったりするらしい。なにそれわかりにくい  

例えば Cloudwatch Logss、CloudWatch のコンソール画面では各ロググループにタグ付けは出来ないしそんな項目すら無いですが  
APIやリソース上は付けることが可能であり、そのタグによりリソースグループや CloudFormaiton で管理できる  
そしてそれは請求分配で管理できる  

<img src="/images/2020/aws/tags/tags-01.png" />  

AWSはそういう UI 上は無くても 実はタグ付をサポートしているものもあったりが結構あったりする  

ただし上記 AWS のサイトを見てみればわかるけど、多いようで意外とサポートされている対象は少ない事がわかる  
この時点で案件単位や用途で利用金額を完全にタグ管理だけで分配するのはかなり難しい事がわかると思う  

### Cost Categories によるタグ等の管理  
AWS アカウントを分けて Organizations で管理を分けても完全にはコスト分配の手間からは逃れられない  
AWS の請求管理画面ではそれらをある程度アカウント単位、契約・料金単位、タグ単位等複雑な条件を組み合わせて分配管することも出来る  

<img src="/images/2020/aws/tags/tags-02.png" />  

※アカウント、タグ、サービスについてはなんとなく分けのイメージは出来るが、料金はよくわからない  
  マニュアルもうちょっと情報ください・・・・  

### 妥協して出来る範囲のタグ管理で頑張る
いまいまはタグ管理出来る範囲で頑張るかーと思ってもコンソール上でタグ付けできなやつもあるし  
あったとしてもタグ付け忘れなんて割とよくある話  

そんな人には AWS Condig の required-tags がある  
<i class="fas fa-external-link-alt"></i> [AWS Config - required-tags ](https://docs.aws.amazon.com/config/latest/developerguide/required-tags.html)  

事前にタグ設計をきちんと行い、特定の Key を必ず付ける事を義務付ければこれで管理、検知、自動修復まで出来る  
しかしこれまた監視対象には制限がある  
ただ上記マニュアルは全量が書かれているわけではなく、コンソール上で探してみるともっと多いので下記に記載しておく  

* ACM:Certificate
* APIGateway:RestApi,APIGateway:Stage  
* APIGatewayV2:Api,APIGatewayV2:Stage  
* AutoScaling:AutoScalingGroup,AutoScaling:LaunchConfiguration,AutoScaling:ScalingPlicy,AutoScalingScheduledAction  
* CloudFormation:Stack
* CloudTrail:Trail
* CloudWatch:Alarm
* CodeBuild:Project
* CodePipeline:Pipline
* Config:ResourceCompliance
* DynamoDB:Table
* EC2:CustomerGateway,EC2:EIP,EC2:EgressOnlyInternetGateway,EC2:FlowLog,EC2:Host,EC2:Instance,EC2:InternetGateway,EC2:NatGateway,EC2:NetworkAcl,EC2:NetworkInterface,EC2:RegistersHAInstance,EC2:RouteTable,EC2:SecurityGroup,EC2:Subnet,EC2:VPC,EC2:VPCEndpoint,EC2:VPCEndpointService,EC2:VPCPeeringConnection,EC2:VPNCnnection,EC2:VPNGateway,EC2:Volume
* ElasticBeanstalk:Application,ElasticBeanstalk:Application:Version,ElasticBeanstalk:Environment
* ElasticLoadBalancing:LoadBalancer,ElasticLoadBalancingV2:LoadBalancer
* Elasticksearch:Domain
* IAM:Group,IAM:Plicy,IAM:Role,IAM:User
* KMS:Key
* Lambda:Funstion
* QLDB:Ledger
* RDS:DBCluster,RDS:DBClusterSnapshot,RDS:RDS:DBInstance,RDS:DBSecurityGroup,RDS:DBSnapshot,RDS:DBSubnetGroup,RDS:EventSubscription
* Redshift:Cluster,Redshift:ClusterParameterGroup,Redshift:ClusterSecurityGroup,Redshift:ClusterSnapshot,Redshift:ClusterSubnetGroup,Redshift:EventSubscription
* S3:AccountPublicAccessBlock,S3:Bucket
* SNS:Topic
* SQS:Queue
* SSM:AssosiationCompliance,SSM:ManagedInstanceInventory,SSM:PatchCompliance
* SecretManager:Secret
* ServiceCatalog:CloudFormationProduct,ServiceCatalog:CloudFormationProvisonedProduct
* ShildRegional:Protection
* WAFRegional:RateBasedRule,WAFRegional:Rule,WAFRegional:RyuleGroup,WAFRegional:WebACL
* WAFV2:IPset,WAFV2:ManagedRuleSet,WAFV2:RegexPatternSet,WAFV2:RuleGroup,WAFV2:WebACL
* XRay:EncriptionConfig

この微妙に 分配タグと揃ってない感じよね。。。  

---

なんにしてもタグで管理するには手間がかかる上に利用するAWSサービスによっては旨味が少ない  
最初に結論付けたように、費用を分けたいなら AWS アカウントを分けた方が無難  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="https://www.amazon.co.jp/Amazon-Services%E3%82%A4%E3%83%B3%E3%83%95%E3%83%A9%E3%82%B5%E3%83%BC%E3%83%93%E3%82%B9%E6%B4%BB%E7%94%A8%E5%A4%A7%E5%85%A8-%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0%E6%A7%8B%E7%AF%89-%E8%87%AA%E5%8B%95%E5%8C%96%E3%80%81%E3%83%87%E3%83%BC%E3%82%BF%E3%82%B9%E3%83%88%E3%82%A2%E3%80%81%E9%AB%98%E4%BF%A1%E9%A0%BC%E5%8C%96-impress/dp/4295006653/ref=as_li_ss_il?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&dchild=1&keywords=aws&qid=1589273493&sr=8-5&linkCode=li3&tag=sinokyoufu-22&linkId=18f1847037237e57a246ad34cc51bf8a&language=ja_JP" target="_blank"><img border="0" src="//ws-fe.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=4295006653&Format=_SL250_&ID=AsinImage&MarketPlace=JP&ServiceVersion=20070822&WS=1&tag=sinokyoufu-22&language=ja_JP" ></a><img src="https://ir-jp.amazon-adsystem.com/e/ir?t=sinokyoufu-22&language=ja_JP&l=li3&o=9&a=4295006653" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />
</div>
{{< /rawhtml >}}
