+++
title = "EC2 ImageBuilder 検証"
tags = ["aws"]
categories = ["SE"]
date = "2019-12-11"
description = "Amazon EC2 Image Builder ってどんな感じ？"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## EC2 Image Builder
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://aws.amazon.com/jp/image-builder/" data-iframely-url="//cdn.iframe.ly/eKTphMK?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

現在は下記の OS のみサポート  

> Image Builder は、Amazon Linux 2 および Windows Server 2012、2016、および 2019 をサポートしています。

### 料金  
> Image Builder は、イメージを作成、保存、共有するために使用する、基盤の AWS リソースにかかるコストを別にすれば、無償で提供されます。  

だけどイメージの保存時のストレージ量とかには課金されます。  
このへんはバックアップと一緒  

## ゴールデンイメージの作成
<i class="fas fa-external-link-alt"></i> [EC2 Image BuilderによるOSイメージビルドパイプラインの自動化](https://aws.amazon.com/jp/blogs/news/automate-os-image-build-pipelines-with-ec2-image-builder/)  

作業IAMユーザーには下記権限を与える  
まだサービスポリシーが無かったのでインラインポリシーを作成しておく  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "imagebuilder:Get*",
        "imagebuilder:List*",
        "imagebuilder:Create*",
        "imagebuilder:Delete*",
        "imagebuilder:StartImagePipelineExecution"
      ],
      "Resource": [
        "arn:aws:imagebuilder:ap-northeast-1:778686969569:*"
      ]
    }
  ]
}
```

なんか `imagebuilder:*` でいい気もする  
あと `EC2FullAccess` も念の為つけておこう  

### ビルドコンポーネントの設定
ビルド時に実行するコマンドを設定できる。Ansible とか server spec 使わせろや  

<img src="/images/2019/aws-ec2/imagebuild-01.png" />  
<img src="/images/2019/aws-ec2/imagebuild-02.png" />  
<img src="/images/2019/aws-ec2/imagebuild-03.png" />  

必要なパッケージをインストールし、 Ansible のプレイブックや server spec を codecommit 等からダウンロードし実行が楽かな？  
誰権限で実行されるんだろう？  
`ec2-user` なのか、それが `sudo` で実行するのか？  

Amazon がデフォルトで用意しているサンプルを見てみると  

```
phases:
  - name: build
    steps:
      - name: InstallDockerCE
        action: ExecuteBash
        inputs:
          commands:
            - sudo amazon-linux-extras install docker
            - sudo systemctl start docker
            - sudo systemctl enable docker
            - sudo usermod -a -G docker ec2-user

      - name: RebootToEnablePermissions
        action: Reboot
```

普通に `sudo` している  

### ビルドパイプラインの作成
<img src="/images/2019/aws-ec2/imagebuild-04.png" />  

AmazonLinux を選択し標準のAMIにしておく  

<img src="/images/2019/aws-ec2/imagebuild-05.png" />  
<img src="/images/2019/aws-ec2/imagebuild-06.png" />  

> Initiate a new image build when there are updates to your selected image version.  

選択した OS に最新パッチバージョンが出た場合に利用イメージを更新するかチェックがあるので選択  

作成したビルドコンポーネントを指定  
オリジナルのコンポーネント内でビルドとテストを実行しているのでテストは空欄のまま  

<img src="/images/2019/aws-ec2/imagebuild-07.png" />  

buildpipline 名と ビルドを実行し EC2 に紐付ける IAM を指定  
ロールには `EC2InstanceProfileForImageBuilder` 権限を割り当てておく  

<img src="/images/2019/aws-ec2/imagebuild-08.png" />  

起動インスタンスタイプ、VPC、セキュリティグループ、キーペア、必要により起動ログをS3に出す  

<img src="/images/2019/aws-ec2/imagebuild-09.png" />  
<img src="/images/2019/aws-ec2/imagebuild-10.png" />  
<img src="/images/2019/aws-ec2/imagebuild-11.png" />  
<img src="/images/2019/aws-ec2/imagebuild-12.png" />  

作成されるイメージ名を設定し  

<img src="/images/2019/aws-ec2/imagebuild-13.png" />  
<img src="/images/2019/aws-ec2/imagebuild-14.png" />  

最後の Launch permissions は AMI を公開する気が無いのであれば private にしておく  

### Recipes の変更
pipeline 作成後、 components を変更したり、定義内容を変更したい場合は Recipes から変更する  
こちらもリビジョン管理となっており、変更し新しい pipline が作成する必要がある  

<img src="/images/2019/aws-ec2/imagebuild-15.png" />  

## イメージの作成  

### Piplineの実行  
<img src="/images/2019/aws-ec2/imagebuild-16.png" />  

適当な手順のイメージだけど結構時間かかる  

<img src="/images/2019/aws-ec2/imagebuild-17.png" />  
<img src="/images/2019/aws-ec2/imagebuild-18.png" />  

とりあえず出来た  

---

## 所感
今までも EC2 作成時に プログラムを実行させる設定を埋め込んだり、 cloud-init で起動時に初期設定を行ったりは出来てたけど  
それらポイントポイントで行っていた分散処理を CI/CD ライクにサーバイメージを作成できるようになったのは大きい  

厳密にやってることは cloud-init を初回起動時に実行してるのとさほど変わらないんだけど、  
こちらは実行した結果をイメージ化してくれている  

イメージ作成手順の更新はリビジョン管理されているため、管理上は便利だけど手順上は手間ってのはあるけどいつでも気軽に更新できるのはいいね  
AMI でしかバックアップが出来なかった Redhat OS とか他の OS も早く対応してくれるといいな  

しかし バックアップマネージャー、ライフサイクルマネージャー、イメージビルダーと  
近い領域で似たようなサービスが横並びしているため、どっかで統合か連携されそう  

## 気になった点

* イメージ作成失敗時のエラーがわかりにくい  

```
SSM execution '3918307a-ef3a-44bc-bfac-b81540ba7fee' failed with status = 'Failed' and failure message = 'Step fails when it is verifying the command has completed. 
Command 5adcaa39-5eca-4a96-8322-35afe3d0fd94 returns unexpected invocation result: {Status=[Failed], ResponseCode=[1], 
Output=[{ "executionId": "0ac4aa1a-1d5a-11ea-bba1-06fa12f2d378", "status": "failed", "failedStepCount": 1, "executedStepCount": 1, "failureMessage": "Document TOE_2019-12-13_03-38-35_UTC-0_0ac4aa1a-1d5a-11ea-bba1-06fa12f2d378/study-imagebuild-doc_1073741825 failed!", 
"logUrl": "/tmp/TOE_2019-12-13_03-38-35_UTC-0_0ac4aa1a-1d5a-11ea-bba1-06fa12f2d378" } 
----------ERROR------- failed to run commands: exit status 1], OutputPayload=[{"Status":"Failed","ResponseCode":1,"Output":"{\n \"executionId\": \"0ac4aa1a-1d5a-11ea-bba1-06fa12f2d378\",\n \"status\": \"failed\",\n \"failedStepCount\": 1,\n \"executedStepCount\": 1,\n \"failureMessage\": \"Document TOE_2019-12-13_03-38-35_UTC-0_0ac4aa1a-1d5a-11ea-bba1-06fa12f2d378/study-imagebuild-doc_1073741825 failed!\",\n \"logUrl\": \"/tmp/TOE_2019-12-13...'
```

ちなみにこのエラーは `yum install -y` にしてなかったからのエラー  


* 古い recioes で作成したいイメージが削除出来ない  
images にある 失敗も成功も 「Delete version」 がグレーアウトしてて削除できない？  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797392568/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/61N%2BYkPAOtL._SL160_.jpg" alt="Amazon Web Services 業務システム設計・移行ガイド (Informatics&IDEA)" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
