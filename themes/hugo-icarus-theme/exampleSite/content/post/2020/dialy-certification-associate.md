+++
title = "AWS Certification アーキテクト、SysOps、デベロッパーの Associate 取得に勉強した事"
tags = ["資格"]
categories = ["diary"]
date = "2020-04-23"
description = "AWS Certification の Associate 3種をそれぞれ自力で勉強した事メモ"
menu = ""
banner = "banners/banner_school.jpg"
images = []
+++

<!--more-->

## AWS 認定資格の Associate 3種
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/certification/certified-solutions-architect-associate/" data-iframely-url="//cdn.iframe.ly/rg094Fb?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/certification/certified-developer-associate/" data-iframely-url="//cdn.iframe.ly/UdwicS6?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/certification/certified-sysops-admin-associate/" data-iframely-url="//cdn.iframe.ly/OJri8fp?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

## まずやること
できるだけお金をかけずに、ギリギリでも良いから資格の合格を目指す  
そのため参考書等試験対策本は買わず、特に有料の学習コースも受けずに資格取得を目指しますが最低限下記は必要  

**模擬試験を受ける**  
模擬試験は1試験2000円くらいかな？  
アーキテクトは 30問、デベロッパーと SysOps は20問  
得点と理解度に関するレポートは来るけど回答は教えてくれない  

AWS認定試験の雰囲気や出題傾向、問題範囲、問われる知識を確認するためには最低限必要だと思う  
ノー勉でもいいのでとりあえず受けて、出題問題をメモし、復習等出来るようにする  

各資格案内のサイト内にある試験サンプルもチェックしておく  
こちらは回答も解説もあるので理解につながる  

**AWS を触る**  
基本となる SE 業務知識ももちろんですが、AWS を業務等で使ったこと無いのに試験を受けるのは結構無謀  
私は仕事上使うので会社を利用しますが、個人の場合は無料期間を利用したりして  
模擬試験の内容を実サービス上で確認したり、機能、出来ること、動きを把握するためにAWSは使う  

資格を取ることが目的で、暗記力に自身があるのであればドキュメントを読み漁るでも良いと思う  

## 模擬試験からみる出題傾向と学習するAWSサービス

### ソリューションアーキテクト  
※ 2018 年時の模擬試験内容に対するものになります  

<i class="fas fa-pen"></i> AWS 基礎知識  
[リージョンとアベイラビリティーゾーン](https://aws.amazon.com/jp/about-aws/global-infrastructure/regions_az/)  
[AWS アクセスキーを管理するためのベストプラクティス](https://docs.aws.amazon.com/ja_jp/general/latest/gr/aws-access-keys-best-practices.html)  
[AWS IAM とは](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/introduction.html)  
[AWS IAM 一時的セキュリティ認証情報(AWS Security Token Service)](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_credentials_temp.html)  
[AWS IAM ユーザーのアクセスキーの管理](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_credentials_access-keys.html)  

<i class="fas fa-pen"></i> Amazon S3 に関する知識  
[Amazon S3 とは](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/Welcome.html)  
[Amazon S3 ストレージクラス](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/storage-class-intro.html)  
[Amazon S3 Glacier データモデル](https://docs.aws.amazon.com/ja_jp/amazonglacier/latest/dev/amazon-glacier-data-model.html)  
[Amazon S3 Glacier アーカイブの取得](https://docs.aws.amazon.com/ja_jp/amazonglacier/latest/dev/downloading-an-archive-two-steps.html)  
[Amazon S3 バージョニングの使用](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/Versioning.html)  
[Amazon S3 オブジェクトのライフサイクル管理](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/object-lifecycle-mgmt.html)  
[Amazon S3 パフォーマンスの最適化](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/optimizing-performance.html)  
[Amazon S3 バケット内のオブジェクトキー名のプレフィックスとして、ランダム化されたハッシュを使用する必要がありますか?](https://aws.amazon.com/jp/premiumsupport/knowledge-center/s3-object-prefix-naming/)  
[Amazon S3 Cross-Origin Resource Sharing (CORS)](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/cors.html)  

<i class="fas fa-pen"></i> Amazon EFS に関する知識  
[Amazon Elastic File System とは](https://docs.aws.amazon.com/ja_jp/efs/latest/ug/whatisefs.html)  
[Amazon EFS での汎用 I/O パフォーマンスモードと最大 I/O パフォーマンスモードの違いを教えてください。](https://aws.amazon.com/jp/premiumsupport/knowledge-center/linux-efs-performance-modes/)  

<i class="fas fa-pen"></i> EC2 / VPC 関連  
[Amazon VPC とは?](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/what-is-amazon-vpc.html)  
[Amazon VPC とサブネット](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/VPC_Subnets.html)  
[Amazon VPC のセキュリティグループ](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/VPC_SecurityGroups.html)  
[Amazon VPC NAT ゲートウェイ](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/vpc-nat-gateway.html)  
[Amazon VPC エンドポイント](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/vpc-endpoints.html)  
[Amazon EC2 とは](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/concepts.html)  
[Amazon EC2 インスタンスストア](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/InstanceStorage.html)  
[Amazon EC2 リザーブドインスタンス](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ec2-reserved-instances.html)  
[Amazon EC2 Auto Scaling とは](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html)  
[Amazon EC2 Auto Scaling の動的なスケーリング](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/as-scale-based-on-demand.html)  
[Amazon EC2 Amazon Elastic Block Store (Amazon EBS)](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/AmazonEBS.html)  
[Amazon EC2 Amazon EBS スナップショット](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/EBSSnapshots.html)  
[Amazon EC2 Amazon EBS ボリュームの種類](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ebs-volume-types.html)  
[Amazon EC2 インスタンスメタデータとユーザーデータ](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ec2-instance-metadata.html)  
[Amazon EC2 の耐障害性](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/disaster-recovery-resiliency.html)  
[Elastic Load Balancing とは](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/userguide/what-is-load-balancing.html)  

<i class="fas fa-pen"></i> CloudFront に関する知識  
[Amazon CloudFront とは](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)  
[Amazon CloudFront コンテンツのキャッシュの最適化および可用性](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/ConfiguringCaching.html)  

<i class="fas fa-pen"></i> Amazon RDS に関する知識  
[Amazon Relational Database Service (​Amazon RDS) とは?](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/UserGuide/Welcome.html)  
[Amazon RDS のクォータと制約](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/UserGuide/CHAP_Limits.html)  
[Amazon RDS Amazon Aurora とは](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html)  

<i class="fas fa-pen"></i> Amazon DynamoDB に関する知識  
[Amazon DynamoDB とは](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Introduction.html)  
[Amazon DynamoDB Auto Scaling によるスループットキャパシティーの自動管理](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/AutoScaling.html)  

<i class="fas fa-pen"></i> AWS Elastic Beanstalk に関する知識  
[AWS Elastic Beanstalk とは](https://docs.aws.amazon.com/ja_jp/elasticbeanstalk/latest/dg/Welcome.html)  

<i class="fas fa-pen"></i> AWS CloudFormation に関する知識  
[AWS CloudFormation とは](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/Welcome.html)  

<i class="fas fa-pen"></i> AWS Code 関連  
[AWS CodeBuild とは](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/welcome.html)  
[AWS CodeDeploy とは](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/welcome.html)  

<i class="fas fa-pen"></i> AWS KMS に関する知識  
[AWS Key Management Service とは](https://docs.aws.amazon.com/ja_jp/kms/latest/developerguide/overview.html)  
[AWS KMS Amazon Elastic Block Store (Amazon EBS) で AWS KMS を使用する方法](https://docs.aws.amazon.com/ja_jp/kms/latest/developerguide/services-ebs.html)  

<i class="fas fa-pen"></i> AWS ACM に関する知識  
[AWS Certificate Manager とは](https://docs.aws.amazon.com/ja_jp/acm/latest/userguide/acm-overview.html)  

<i class="fas fa-pen"></i> Amazon API Gateway に関する知識  
[Amazon API Gateway とは](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/welcome.html)  

<i class="fas fa-pen"></i> AWS Storage Gateway に関する知識  
[AWS Storage Gateway とは](https://docs.aws.amazon.com/ja_jp/storagegateway/latest/userguide/WhatIsStorageGateway.html)  

<i class="fas fa-pen"></i> Amazon Redshift に関する知識  
[Amazon Redshift とは?](https://docs.aws.amazon.com/ja_jp/redshift/latest/mgmt/welcome.html)  
[Amazon Redshift 拡張された VPC のルーティング](https://docs.aws.amazon.com/ja_jp/redshift/latest/mgmt/enhanced-vpc-routing.html)  

<i class="fas fa-pen"></i> Amazon Simple Queue Service に関する知識  
[Amazon Simple Queue Service とは](https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)  

<i class="fas fa-pen"></i> AWS Lambda に関する知識  
[AWS Lambda とは](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/welcome.html)  

<i class="fas fa-pen"></i> Amazon SNS に関する知識  
[Amazon SNS とは](https://docs.aws.amazon.com/ja_jp/sns/latest/dg/welcome.html)  

<i class="fas fa-pen"></i> Amazon Kinesis に関する知識  
[Amazon Kinesis ビデオストリーム とは?](https://docs.aws.amazon.com/ja_jp/kinesisvideostreams/latest/dg/what-is-kinesis-video.html)  
[Amazon Kinesis Data Streams とは?](https://docs.aws.amazon.com/ja_jp/streams/latest/dev/introduction.html)  
[Amazon Kinesis Data Streams のよくある質問](https://aws.amazon.com/jp/kinesis/data-streams/faqs/)  
[Amazon Kinesis Data Firehose とは?](https://docs.aws.amazon.com/ja_jp/firehose/latest/dev/what-is-this-service.html)  

<i class="fas fa-pen"></i> Amazon Elastic Container Service に関する知識  
[Amazon Elastic Container Service とは](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/Welcome.html)  
[Amazon ECS タスク用の IAM ロール](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/task-iam-roles.html)  

<i class="fas fa-pen"></i> Amazon ElastiCache に関する知識  
[Amazon ElastiCache](https://docs.aws.amazon.com/elasticache/index.html)  

<i class="fas fa-pen"></i> Amazon CloudWatch に関する知識  
[Amazon CloudWatch とは](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)  

### デベロッパー
※ 2019 年時の模擬試験内容に対するものになります  
※ アーキテクト等他試験と重複している内容は割愛  

<i class="fas fa-pen"></i> AWS 基礎知識  
[AWS でのエラー再試行とエクスポネンシャルバックオフ](https://docs.aws.amazon.com/ja_jp/general/latest/gr/api-retries.html)  
[Blue/Greenデプロイとは？](https://aws.typepad.com/sajp/2015/12/what-is-blue-green-deployment.html)  
[サーバーレス](https://aws.amazon.com/jp/serverless/)  
[AWS での構築ツール](https://aws.amazon.com/jp/tools/)  
[署名バージョン 4 を使用してAWS リクエストに署名する](https://docs.aws.amazon.com/ja_jp/general/latest/gr/sigv4_signing.html)  

<i class="fas fa-pen"></i> Amazon ElastiCache に関する知識  
[Amazon ElastiCache キャッシュ戦略](https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/mem-ug/Strategies.html)  

<i class="fas fa-pen"></i> AWS Elastic Beanstalk に関する知識  
[AWS Elastic Beanstalk 新しい環境の作成ウィザード](https://docs.aws.amazon.com/ja_jp/elasticbeanstalk/latest/dg/environments-create-wizard.html)  
[AWS Elastic Beanstalk 環境へのアプリケーションのデプロイ](https://docs.aws.amazon.com/ja_jp/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html)  

<i class="fas fa-pen"></i> AWS Code 関連  
[AWS CodeBuild のビルド仕様に関するリファレンス](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/build-spec-ref.html)  
[AWS CodeBuild のアクセス許可に関するリファレンス](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/auth-and-access-control-permissions-reference.html)  

<i class="fas fa-pen"></i> Amazon S3 に関する知識  
[Amazon S3 マルチパートアップロードの概要](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/mpuoverview.html)  
[Amazon S3 のセキュリティのベストプラクティス](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/security-best-practices.html)  

<i class="fas fa-pen"></i> Amazon RDS に関する知識  
[Amazon RDS リードレプリカ](https://aws.amazon.com/jp/rds/features/read-replicas/)  
[Amazon RDS で機密データを保護するためのベストプラクティスの適用](https://aws.amazon.com/jp/blogs/news/applying-best-practices-for-securing-sensitive-data-in-amazon-rds/)  
[Amazon RDS リソースの暗号化](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/UserGuide/Overview.Encryption.html#Overview.Encryption.Limitations)  

<i class="fas fa-pen"></i> Amazon Simple Queue Service に関する知識  
[Amazon SQS に関するよくある質問](https://aws.amazon.com/jp/sqs/faqs/)  
[Amazon SQS ショートポーリングとロングポーリング](https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html)  
[Amazon SQS可視性タイムアウト](https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html)  

<i class="fas fa-pen"></i> Amazon CloudWatch に関する知識  
[Amazon CloudWatch で高解像度のカスタムメトリクスおよびアラームを導入](https://aws.amazon.com/jp/about-aws/whats-new/2017/07/amazon-cloudwatch-introduces-high-resolution-custom-metrics-and-alarms/)  
[Amazon CloudWatch Logs とは](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)  
[Amazon CloudWatch でのアラームの使用](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)  

<i class="fas fa-pen"></i> AWS CloudTrail に関する知識  
[AWS CloudTrail とは](https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)  

<i class="fas fa-pen"></i> AWS Lambda に関する知識  
[AWS Lambda 環境変数の使用](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/configuration-envvars.html)  
[AWS Lambda 関数のエイリアス](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/configuration-aliases.html)  
[AWS Lambda 関数スケーリング](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/invocation-scaling.html)  
[AWS Lambda 関数を使用する際のベストプラクティス](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/best-practices.html)  
[AWS Lambda トリガーを使用したユーザープールワークフローのカスタマイズ](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/cognito-user-identity-pools-working-with-aws-lambda-triggers.html)  
[AWS Lambda レイヤー](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/configuration-layers.html)  

<i class="fas fa-pen"></i> AWS System Manager に関する知識  
[AWS Systems Manager とは何ですか?](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/what-is-systems-manager.html)  
[AWS Systems Manager パラメータストア](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-parameter-store.html)  
[AWS Systems Manager SSM エージェント の使用](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/ssm-agent.html)  

<i class="fas fa-pen"></i> Amazon API Gateway に関する知識  
[Amazon API Gateway ステージ変数の使用](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/amazon-api-gateway-using-stage-variables.html)  
[Amazon API Gateway Lambda オーソライザーの使用](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html)  
[Amazon API Gateway で統合レスポンスを設定する](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/api-gateway-integration-settings-integration-response.html)  
[Amazon API Gateway API に IAM 認証を有効化するにはどうすればよいですか?](https://aws.amazon.com/jp/premiumsupport/knowledge-center/iam-authentication-api-gateway/)  
[Amazon API Amazon Cognito ユーザープール をオーソライザーとして使用して REST API へのアクセスを制御する](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html)  
[Amazon API Gateway Canary リリースのデプロイをセットアップする](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/canary-release.html)  

<i class="fas fa-pen"></i> Amazon DynamoDB に関する知識  
[Amazon DynamoDB ストリーム を使用したテーブルアクティビティのキャプチャ](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Streams.html)  
[Amazon DyanmoFB インメモリアクセラレーションと DynamoDB Accelerator (DAX)](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/DAX.html)  

<i class="fas fa-pen"></i> AWS Secrets Manager に関する知識  
[AWS Secrets Manager とは](https://docs.aws.amazon.com/ja_jp/secretsmanager/latest/userguide/intro.html)  

<i class="fas fa-pen"></i> AWS AppSync に関する知識  
[AWS App Syncシステムの概要とアーキテクチャ](https://docs.aws.amazon.com/ja_jp/appsync/latest/devguide/system-overview-and-architecture.html)  

<i class="fas fa-pen"></i> CloudFront に関する知識  
[CloudFront Lambda@Edge を使用したエッジでのコンテンツのカスタマイズ](https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/lambda-at-the-edge.html)  

<i class="fas fa-pen"></i> Amazon Cognito に関する知識  
[Amazon Cognito とは](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/what-is-amazon-cognito.html)  
[Amazon Cognito ユーザープール](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/cognito-user-identity-pools.html)  
[Amazon Cognito ID プール (フェデレーティッドアイデンティティ)](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/cognito-identity.html)  

<i class="fas fa-pen"></i> AWS Config に関する知識  
[AWS Config とは](https://docs.aws.amazon.com/ja_jp/config/latest/developerguide/WhatIsConfig.html)  

<i class="fas fa-pen"></i> Amazon Elasticsearch Service に関する知識  
[Amazon Elasticsearch Service とは](https://docs.aws.amazon.com/ja_jp/elasticsearch-service/latest/developerguide/what-is-amazon-elasticsearch-service.html)  
[Amazon ES Kibana と Logstash](https://docs.aws.amazon.com/ja_jp/elasticsearch-service/latest/developerguide/es-kibana.html#es-managedomains-kibana)  

<i class="fas fa-pen"></i> Amazon Inspector に関する知識  
[Amazon Inspector とは](https://docs.aws.amazon.com/ja_jp/inspector/latest/userguide/inspector_introduction.html)  

<i class="fas fa-pen"></i> Amazon EventBridge に関する知識  
[Amazon EventBridge とは?](https://docs.aws.amazon.com/ja_jp/eventbridge/latest/userguide/what-is-amazon-eventbridge.html)  

<i class="fas fa-pen"></i> EC2 / VPC 関連  
[Amazon EC2 フリート の設定戦略](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ec2-fleet-configuration-strategies.html)  

### SysOps アドミニストレーター
※ 2019 年時の模擬試験内容に対するものになります  
※ アーキテクト等他試験と重複している内容は割愛  

<i class="fas fa-pen"></i> AWS 基礎知識  
[責任共有モデル](https://aws.amazon.com/jp/compliance/shared-responsibility-model/)  
[IAM SAML 2.0 フェデレーティッドユーザーが AWS マネジメントコンソールにアクセス可能にする](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_roles_providers_enable-console-saml.html)  
[AWS Artifact とは](https://docs.aws.amazon.com/ja_jp/artifact/latest/ug/what-is-aws-artifact.html)  
[AWS Trusted Advisor Best Practice Checks](https://aws.amazon.com/jp/premiumsupport/technology/trusted-advisor/best-practice-checklist/)  
[AWS Health とは](https://docs.aws.amazon.com/ja_jp/health/latest/ug/what-is-aws-health.html)  

<i class="fas fa-pen"></i> EC2 / VPC 関連  
[Amazon VPC Egress-Only インターネットゲートウェイ](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/egress-only-internet-gateway.html)  
[Amazon VPC のよくある質問](https://aws.amazon.com/jp/vpc/faqs/)  
[Amazon VPC ピア機能とは](https://docs.aws.amazon.com/ja_jp/vpc/latest/peering/what-is-vpc-peering.html)  
[どうすれば、オフィスネットワークとAmazon Virtual Private Cloud (VPC) の間の安全な接続を実現できますか？](https://aws.amazon.com/jp/premiumsupport/knowledge-center/create-connection-vpc/)  
[Amazon EC2 Auto Scaling インスタンスおよびグループのモニタリング](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/as-monitoring-features.html)  
[Amazon EC2 Auto Scaling が正常でないインスタンスを終了させなかったのはなぜですか?](https://aws.amazon.com/jp/premiumsupport/knowledge-center/auto-scaling-terminate-instance/)  
[Classic Load Balancer のスティッキーセッションを設定する](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/classic/elb-sticky-sessions.html)  
[Amazon EC2 オンデマンドインスタンス](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html#ec2-on-demand-instances-limits)  
[Amazon EC2 スポットインスタンス](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/using-spot-instances.html)  

<i class="fas fa-pen"></i> Amazon Route 53 に関連する知識  
[Amazon Route 53 とは](https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/Welcome.html)  
[ルーティングポリシーの選択](https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/routing-policy.html)  

<i class="fas fa-pen"></i> AWS Code 関連  
[AWS CodePipeline とは](https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/welcome.html)  

<i class="fas fa-pen"></i> AWS System Manager に関する知識  
[AWS Systems Manager Patch Manager](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/systems-manager-patch.html)  
[AWS Systems Manager Parameter Store を使用して最新の Amazon Linux AMI IDを取得する](https://aws.amazon.com/jp/blogs/news/query-for-the-latest-amazon-linux-ami-ids-using-aws-systems-manager-parameter-store/)  

<i class="fas fa-pen"></i> AWS Snowball デバイス に関する知識  
[AWS Snowball デバイスとは](https://docs.aws.amazon.com/ja_jp/snowball/latest/ug/whatissnowball.html)  
[AWS Snowball のよくある質問](http://aws.amazon.com/jp/snowball/faqs/)  
[AWS Snowball デバイスの相違点](https://docs.aws.amazon.com/ja_jp/snowball/latest/ug/device-differences.html)  
[AWS Snowball 料金表](https://aws.amazon.com/jp/snowball/pricing/)  

<i class="fas fa-pen"></i> AWS Storage Gateway に関する知識  
[AWS Storage Gateway の料金表](https://aws.amazon.com/jp/storagegateway/pricing/)  

<i class="fas fa-pen"></i> AWS CloudFormation に関する知識  
[AWS CloudFormation 組み込み関数リファレンス](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html)  
[AWS CloudFormation スタックを更新するときに、 Auto Scaling グループを正しく更新する方法を教えてください。](https://aws.amazon.com/jp/premiumsupport/knowledge-center/auto-scaling-group-rolling-updates/)  
[AWS CloudFormation リソース属性リファレンス](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-product-attribute-reference.html)  

<i class="fas fa-pen"></i> AWS WAF に関する知識  
[AWS WAF、AWS Shield、AWS Firewall Manager とは](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/what-is-aws-waf.html)  

<i class="fas fa-pen"></i> Amazon Simple Queue Service に関する知識  
[Amazon SQS に基づくスケーリング](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/as-using-sqs-queue.html)  

<i class="fas fa-pen"></i> AWS Service Catalog に関する知識  
[AWS Service Catalog とは?](https://docs.aws.amazon.com/ja_jp/servicecatalog/latest/adminguide/introduction.html)  

<i class="fas fa-pen"></i> Amazon SES に関する知識  
[Amazon SES とは](https://docs.aws.amazon.com/ja_jp/ses/latest/DeveloperGuide/Welcome.html)  

<i class="fas fa-pen"></i> Amazon MQ に関する知識  
[Amazon MQ とは](https://docs.aws.amazon.com/ja_jp/amazon-mq/latest/developer-guide/welcome.html)  

---

感覚としては模擬試験で満点取れるくらいの理解力になれば本番で7割くらいは取れる  
駄目だった、心配な人はさらに対策本買ったり、オンラインセミナー受けたりして学習すれば合格は出来るんでしょうかね  

デベロッパー、SysOps はアーキテクトで求められる基礎知識にプラスアルファの実務的な事を問われるためにまずはアーキテクトの合格を目指すほうが効率がいい  
こうみるとアーキテクトに求められる知識範囲はやはり広く、デベロッパー、SysOps は実業務観点でそうしたらいい？という観点が問われている気がする  

各試験ともに重点的に問われている項目は偏りがあるのでそこは落とさないように気をつける  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
