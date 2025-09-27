# Terraform から複数の AWS アカウントを使い分ける方法

Terraform から複数の AWS アカウントを使い分けたい場合、AWS CLI のプロファイル機能と Terraform の provider 設定を組み合わせます。Mac でも問題なく使えます。

## ✅ 手順

### 1. AWS CLI プロファイルを設定

まずは Mac の `~/.aws/credentials` または `~/.aws/config` に複数のプロファイルを作ります。

```bash
aws configure --profile account1
aws configure --profile account2
```

~/.aws/credentials

```bash
[account1]
aws_access_key_id = XXXXXXXXXXXXXXXXXX
aws_secret_access_key = XXXXXXXXXXXXXXXXXXXXXXXXX

[account2]
aws_access_key_id = YYYYYYYYYYYYYYYYYY
aws_secret_access_key = YYYYYYYYYYYYYYYYYYYYYYYYY

~/.aws/config

[profile account1]
region = us-east-1

[profile account2]
region = ap-northeast-1
```

Terraform のプロバイダで profile を指定
Terraform の provider "aws" に profile を書くと、そのアカウントを使ってデプロイできます。

例）main.tf

```bash
# デフォルトのプロバイダ（account1）
provider "aws" {
  region  = "us-east-1"
  profile = "account1"
}

# 別アカウント用のプロバイダ（account2）
provider "aws" {
  alias   = "account2"
  region  = "ap-northeast-1"
  profile = "account2"
}

# account2 にリソースを作成する例
resource "aws_s3_bucket" "example" {
  provider = aws.account2
  bucket   = "my-example-bucket-terraform"
}
```

Terraform 実行

```bash
terraform init
terraform plan
terraform apply
```

補足

AWS_PROFILE 環境変数を使う方法もあります：

```bash
AWS_PROFILE=account2 terraform apply
```
