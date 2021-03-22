+++
title = "Amazon S3 のセキュリティをガチガチに考えてみる"
tags = ["aws"]
categories = ["SE"]
date = "2019-10-11"
description = "Amazon S3 の設定をどこまでクローズにできるか考えてみる"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## ブロックパブリックアクセスを確認する  
<i class="fas fa-external-link-alt"></i> [Amazon S3 ブロックパブリックアクセスの使用](https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/access-control-block-public-access.html)  

> Amazon S3 には、バケットとアカウント用のブロックパブリックアクセス設定があり、Amazon S3 リソースへのパブリックアクセスを管理するのに役立ちます。デフォルトでは、新しいバケットとオブジェクトはパブリックアクセスを許可しませんが、ユーザーはパブリックアクセスを許可するようにバケットポリシーまたはオブジェクトのアクセス許可を変更できます。Amazon S3 ブロックパブリックアクセス設定は、これらのポリシーとアクセス許可をオーバーライドするため、これらのリソースへのパブリックアクセスを制限できます。Amazon S3 ブロックパブリックアクセスを使用すると、アカウント管理者とバケット所有者は、リソースの作成方法に関係なく、強制される Amazon S3 リソースへのパブリックアクセスを制限するために集中管理を簡単に設定できます。  

あらゆるルールより優先されるパブリックアクセスブロック制御ポリシーデフォルトでは有効可されている  

<img src="/images/2019/aws-s3-security/s3-policy01.png" />  

データの保存先として S3 を利用するのであればこれだけでも十分な気がするけど  
もう少し考えてみる  

## VPC S3 エンドポイントの作成  
<i class="fas fa-external-link-alt"></i> [VPC エンドポイント](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/vpc-endpoints.html)  

サービスの選択  

<img src="/images/2019/aws-s3-security/vpc_create_gwendpoint02.png" />  

利用する VPC の指定  

<img src="/images/2019/aws-s3-security/vpc_create_gwendpoint03.png" />  

アクセスポリシーの設定  
デフォルトだとフルアクセス  

<img src="/images/2019/aws-s3-security/vpc_create_gwendpoint04.png" />  

VPC単位でエンドポイント作成のためある程度は制限されるけど、  
さらに制限したければアクセスするサーバの ローカル IP に絞るのもいいかも  
エンドポイントを作成すればシステム上 VPC 内からリクエスト出す場合は外部に出ていかなくなる  

## S3 バケットポリシー  
S3 のバケットポリシーを設定してアクセス許可を VPC 内の IP からのみに制限する  

<img src="/images/2019/aws-s3-security/s3-policy02.png" />  

ポリシージェネレーターからポリシーを作成  

<img src="/images/2019/aws-s3-security/s3-policy03.png" />  
<img src="/images/2019/aws-s3-security/s3-policy04.png" />  

作成したポリシーを貼り付けて保存  

<img src="/images/2019/aws-s3-security/s3-policy05.png" />  

しかしこれでも IAM で許可権限を与えられているとアクセスは出来てしまう  

## IAM のアクセス拠点制限  
IAM で許可されているユーザーのアクセス許可設定を　S3 同様にカスタマイズする  
IAM よりカスタムポリシーの作成  

<img src="/images/2019/aws-s3-security/s3-policy06.png" />  
<img src="/images/2019/aws-s3-security/s3-policy07.png" />  

作成したポリシーを ロール、グループ、あるいは ユーザーに付与する  

<img src="/images/2019/aws-s3-security/s3-policy08.png" />  

サーバ側からバケットにアクセスすると  

```
[hogehoge@localhost ~]$ aws s3 ls s3://bucket-name/

2017-10-25 19:02:09          8 index.html
[hogehoge@localhost ~]$
```

ちゃんと見えるけど  
その他の端末からアクセスしようとすると  

```
PS C:\Users\user-name\Desktop>  aws s3 ls s3://bucket-name/

An error occurred (AccessDenied) when calling the ListObjectsV2 operation: Access Denied
```

アクセスが拒否されるようになった  

バケットの公開・非公開、バケットポリシーだけではまだ IAM 経由で外部からアクセス出来てしまうため、  
用途とアクセス元が決まっているのであればきちんとアクセス制御したほうがいいね  
IAM の優先順位って結構強いんだね  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392576/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61iiVnqAeGL._SL160_.jpg" alt="Amazon Web Services パターン別構築・運用ガイド 改訂第2版 (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
