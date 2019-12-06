+++
title = "ESC Fargate を利用した CI/CD 環境の構築"
tags = ["aws"]
categories = ["SE"]
date = "2019-12-06"
description = "AWS Fargate + CodePipline を使ってコンテナベースの CD/CD 環境構築 なおこれは最終的には失敗談です"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## AWS Fargate
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/fargate/" data-iframely-url="//cdn.iframe.ly/CMwcW1l?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

### 料金

> Amazon ECS の場合、AWS Fargate の料金は、コンテナイメージのダウンロード (docker pull) を開始した時点から Amazon ECS タスク* が終了するまでに使用された vCPU およびメモリリソースに基づいて計算され、最も近い秒数に切り上げられます。1 分の最低料金が適用されます。  

per vCPU per hour 0.05056USD  
per GB per hour  0.00553USD  

かなり安い  
ECS とで2重に料金体系が発生するということもなさそう  

## ECS Fargate 環境のセットアップ  
<i class="fas fa-external-link-alt"></i> [Fargate を使用した Amazon ECS の使用開始](https://docs.aws.amazon.com/ja_jp/AmazonECS/latest/developerguide/ECS_GetStarted_Fargate.html)  

構築時には下記を追加しておく、権限が強いため作成する人と、実際にアプリケーションをデプロイする人で権限は分けたほうが良さそう  
ドキュメントで 管理者権限か～と言っているくらい要求する権限が多く大変  

* **AmazonVPCFullAccess**  

* **AmazonECS_FullAccess**
    * App Mesh  
    * Application Auto Scaling  
    * Cloud Map  
    * CloudFormation  
    * CloudWatch  
    * CloudWatch Logs  
    * CodeDeploy  
    * EC2  
    * EC2 Auto Scaling  
    * Elastic Container Service  
    * ELB  
    * ELB v2  
    * EventBridge  
    * IAM  
    * Lambda  
    * Route 53  
    * SNS  
    * Systems Manager  

* **AmazonECSTaskExecutionRolePolicy**  
    * CloudWatch Logs  
    * Elastic Container Registry  

* **IAM**  

* **AWSCloudFormationFullAccess**  

```
{
    "Effect": "Allow",
    "Action": [
        "iam:AttachRolePolicy",
        "iam:CreateRole",
        "iam:PutRolePolicy"
    ],
    "Resource": "*"
}
```

### ECSのセットアップ
初期画面からチュートリアルをすすめるとデフォルトで Fargate を利用して ECS クラスターが作成される  

<img src="/images/2019/aws_ecs/fargate-01.png" />  
<img src="/images/2019/aws_ecs/fargate-02.png" />  
<img src="/images/2019/aws_ecs/fargate-03.png" />  
<img src="/images/2019/aws_ecs/fargate-04.png" />  
<img src="/images/2019/aws_ecs/fargate-05.png" />  

作成には最大10分くらいかかる場合もあるらしい  
作成後、サービス表示から ELB アクセスし  

<img src="/images/2019/aws_ecs/fargate-06.png" />  

ELB の URL にアクセスするとサンプルのアプリケーションが表示される  

<img src="/images/2019/aws_ecs/fargate-07.png" />  

## CodeCommit からイメージの作成  
Docker で動かすアプリケーションを用意して 作成した ECS にデプロイする  

### CodeCommitの準備
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/codecommit/" data-iframely-url="//cdn.iframe.ly/sWwN1y2?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

[こちら](/2019/07/16/aws_codecommit/)を参考に既にあるものを利用するのでスキップ  

### Dockerファイルの用意  
テスト用に Hugo アプリケーションを動かしてみる  
リポジトリの TOP には Dcokerfile を用意してソースに含めておく  
下記のは適当に Docker Hub か AWSのドキュメントから持ってきたやつ  

```
FROM ubuntu:18.04

# Install dependencies
RUN apt-get update && \
 apt-get -y install apache2

# Install apache and write hello world message
RUN echo 'Hello World!' > /var/www/html/index.html

# Configure apache
RUN echo '. /etc/apache2/envvars' > /root/run_apache.sh && \
 echo 'mkdir -p /var/run/apache2' >> /root/run_apache.sh && \
 echo 'mkdir -p /var/lock/apache2' >> /root/run_apache.sh && \ 
 echo '/usr/sbin/apache2 -D FOREGROUND' >> /root/run_apache.sh && \ 
 chmod 755 /root/run_apache.sh

EXPOSE 80

CMD /root/run_apache.sh
```

### CodeBuild 用ファイルの用意  
こちらもリポジトリの TOP に配置しておく  
<i class="fas fa-external-link-alt"></i> [CodeBuild の Docker サンプル](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/sample-docker.html)  

ビルドに利用するランタイムバージョンについては下記を参照  
<i class="fas fa-external-link-alt"></i> [CodeBuild に用意されている Docker イメージ](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/build-env-ref-available.html)  

```
version: 0.2

phases:
  install: 
   runtime-versions: 
      docker: 18 
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - $(aws ecr get-login --no-include-email --region $AWS_DEFAULT_REGION)
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...          
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
```

### ECR リポジトリの作成
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/ecr/" data-iframely-url="//cdn.iframe.ly/MnAeNlR?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

操作するための権限は`AmazonEC2ContainerRegistryFullAccess`が必要  
<i class="fas fa-external-link-alt"></i> [Amazon ECR 管理ポリシー](https://docs.aws.amazon.com/ja_jp/AmazonECR/latest/userguide/ecr_managed_policies.html)  

<img src="/images/2019/aws_ecs/fargate-08.png" />  
<img src="/images/2019/aws_ecs/fargate-09.png" />  
<img src="/images/2019/aws_ecs/fargate-10.png" />  

### CodeBuild
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/codebuild/" data-iframely-url="//cdn.iframe.ly/mLvXQOg?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

ビルドプロジェクトの作成  

<img src="/images/2019/aws_ecs/fargate-11.png" />  
<img src="/images/2019/aws_ecs/fargate-12.png" />  
<img src="/images/2019/aws_ecs/fargate-13.png" />  
<img src="/images/2019/aws_ecs/fargate-14.png" />  
<img src="/images/2019/aws_ecs/fargate-15.png" />  

環境変数には `bildspec.yml` に渡す変数を定義する  
値は ECR のレポジトリ名  

* AWS_ACCOUNT_ID  
* AWS_DEFAULT_REGION  
* IMAGE_REPO_NAME  
* IMAGE_TAG  

<img src="/images/2019/aws_ecs/fargate-16.png" />  
<img src="/images/2019/aws_ecs/fargate-17.png" />  

自動作成されたロールに ECR へのアクセス権限 `AmazonEC2ContainerRegistryPowerUser`を追加しておき  
ビルドしてみる  

<img src="/images/2019/aws_ecs/fargate-18.png" />  

成功すれば ECR に イメージが登録される  

<img src="/images/2019/aws_ecs/fargate-19.png" />  

## ECRからECSへのリリース

### ALB の作成
コンテナ用にALBを利用するのでターゲットグループ以外は作成しておく  

### クラスターサービスの作成
クラスターからサービスを追加  

### タスクの作成
タスク定義より作成したイメージでタスクを作成  
ECS + ALB の構成上ターゲットグループはリスニングポート 80/443 でしか作成出来ないためコンテナ登録時は注意する  

### サービスの作成
タスクからサービスの作成を行う  
**サービス内にELBの設定を行う事ができるのは作成時のみのため注意**  
**デプロイ方法の選択（Blue/Green Deploy）も初期作成時のみ設定可能**

## CodePipeline の設定  

### タスク定義  
<i class="fas fa-external-link-alt"></i> [Amazon ECR ソースと、ECS と CodeDeploy 間のデプロイでパイプラインを作成する](https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/tutorials-ecs-ecr-codedeploy.html)  


ECS のクラスタータスクを`taskdef.json` ファイル形式でソースに入れておく  
事前に作成してある ECSタスク定義のJSONから抜粋する 

```
{
    "executionRoleArn": "arn:aws:iam::194180380675:role/ecsTaskExecutionRole",
    "containerDefinitions": [
        {
            "name": "{コンテナ名}",
            "image": "<IMAGE1_NAME>",
            "essential": true,
            "portMappings": [
                {
                    "hostPort": 80,
                    "protocol": "tcp",
                    "containerPort": 80
                }
            ]
        }
    ],
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "networkMode": "awsvpc",
    "cpu": "{コンテナCPUサイズ}",
    "memory": "{コンテナメモリサイズ}",
    "family": "{タスク名}"
}
```

※デプロイ時に cpu や memory の 値を変えても反映はされてなかった、どこまで効いてるんだろ？  

### Appspec定義
CodeDeployで利用する定義ファイルを作成  

```
version: 0.0
Resources:
  - TargetService:
      Type: AWS::ECS::Service
      Properties:
        TaskDefinition: <TASK_DEFINITION>
```

※LBの定義とか書かなくても問題ない、どこまで利用されてるんだろ？  

### buildspecファイルの定義

> Did not find the image definition file imagedefinitions.json in the input artifacts ZIP file. Verify the file is stored in your pipeline's Amazon S3 artifact bucket  


> Unable to access the artifact with Amazon S3 object key 'codepip-ecr/BuildArtif/VkvHOBW' located in the Amazon S3 artifact bucket 'codepipeline-ap-northeast-1-****************'. The provided role does not have sufficient permissions.  

Codepipeline でビルド時に利用される buildspec にアーティファクト定義とimagedefinitions.jsonの定義を追加する  
<i class="fas fa-external-link-alt"></i> [イメージ定義ファイルのリファレンス](https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/file-reference.html#pipelines-create-image-definitions)  


```
version: 0.2

phases:
  install: 
   runtime-versions: 
      docker: 18 
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - $(aws ecr get-login --no-include-email --region $AWS_DEFAULT_REGION)
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...          
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
      - printf '[{"name":"%s","imageUri":"%s"}]' $CONTAINER_NAME $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG > imagedefinitions.json
artifacts:
  files: 
    - imagedefinitions.json
    - $TASKDEF_FILE
```

### CodePipelineの作成
CodeCommit に更新があったら CodeBuild に連携して Image をビルドするようにする  
基本的な話は [こちら](/2019/07/16/aws_codepipeline/) をみておく  

ビルドの設定はこんなかんじ  
イメージビルド用の環境変数を定義し、イメージタグを変数から受け取るようにしておく  
<i class="fas fa-external-link-alt"></i> [変数の操作](https://docs.aws.amazon.com/ja_jp/codepipeline/latest/userguide/actions-variables.html)  

ここではローリングアップデートの手順になる  

<img src="/images/2019/aws_ecs/fargate-pipe-01.png" />  

ビルドステージ  
<img src="/images/2019/aws_ecs/fargate-pipe-02.png" />  

ここでは一つのブランチから２つの image を作成できるよう、 pipline を2つ作成し環境変数で定義を分けている  

* AWS_ACCOUNT_ID  
* AWS_DEFAULT_REGION  
* IMAGE_REPO_NAME  
* IMAGE_TAG  
* CONTAINER_NAME  
* TASKDEF_FILE

Pipeline で指定したロールに ECS への権限が無いため最初はコケる  
ロールに `AWSCodeDeployRoleForECS ` 権限を付与しておく  

### CodeDeployパターン  
Codepipeline のデプロイフェーズを CodeDeploy で行うようにすると 
Blue/Green デプロイが行えるようになるし  
手動で ECR から Deploy したいなという時に ECS をいじらずに済むようになる  

Blue/Green はここではやらない（理解できてない）  


---

## 最終構成  
<img src="/images/2019/aws_ecs/fargate-pipe-03.png" />  

### 課題

<i class="fas fa-chevron-circle-right"></i> **AutoScale考えられてない**  
作るのでいっぱいで本番のAutoScale設定が出来ていない  
後でも作れるので利用状況みて考えよう  

<i class="fas fa-chevron-circle-right"></i> **Blue/Green デプロイ**  
今回は出来なかったが、 Blue/Green デプロイを行うためには何を考えなければ行けないか  
どういう構成にする必要があるか（環境別で ECS のサービスを分けては駄目なんだな）とかが少し見えてきた  
同 Cluster 内に別で検証用 タスクとサービスを作ればいいだけなのでどこかで検証する  

ただ本番と検証をどう LB で切り替えてるのかがよくわかってない  

<i class="fas fa-chevron-circle-right"></i> **デプロイに時間がかかる**  
Dockerfile の中身によるが、git push から ECS へのデプロイ完了までは５分～１０分かかる  
かかりすぎ  
イメージを軽くするとか、ビルド時間を短縮する検討が必要  

<i class="fas fa-chevron-circle-right"></i> **本番手動デプロイの方法**  
あまりECS側の操作は行わせたくなく（IAM権限的に）  
ポチっと CodeDeploy のボタンひとつでデプロイできるようになれればよかったが、そこまで出来てない  
構成をあらかた作り終わった後に「あれ？これ本来の使い方の構成じゃない気がする」と気がついた  

<i class="fas fa-chevron-circle-right"></i> **同様の定義が多すぎる**  
`buildspec.yml`、`appspec.yml`、`taskdef.json`、｀Dockerfile｀ など ポートや LB や コンテナ名やイメージ名や  
記述の重複する箇所が多々あるため、既存の設定も含めどちらが優先されているのか？  
どこまで定義の内容が反映されるのかわかりにくい  
定義がなくてもすんなり行ったり、間違ってても既存の設定のまま上手く動いたりする  


{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392568/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61N%2BYkPAOtL._SL160_.jpg" alt="Amazon Web Services 業務システム設計・移行ガイド (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
