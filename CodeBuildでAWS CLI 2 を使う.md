# CodeBuild で AWS CLI 2 を使う

## はじめに

CodeBuild で使用できる AWS CLI のバージョンはデフォルトでは `1` です。
そのため、 `--output yaml` 等の一部のオプションが使用できません。
そこで、CodeBuild で AWS CLI のバージョンを `2` にアップデートする方法を調べました。

## 概要

[Installing the AWS CLI version 2 on Linux](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html#cliv2-linux-upgrade) に則り `AWS CLI version 2` にアップデートするだけです。

```buildspec.yml
version: 0.2

phases:
  install:
    commands:
      - curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      - unzip awscliv2.zip
      - ls -l /root/.pyenv/shims/aws
      - ./aws/install --bin-dir /root/.pyenv/shims --install-dir /usr/local/aws-cli --update
  build:
    commands:
      - aws --version
```

出力結果抜粋

```log
[Container] 2020/08/20 06:25:10 Running command aws --version
aws-cli/2.0.41 Python/3.7.3 Linux/4.14.186-110.268.amzn1.x86_64 exec-env/AWS_ECS_EC2 exe/x86_64.ubuntu.18
```

## 補足

aws command のパスは以下の通り

```log
[Container] 2020/08/20 06:25:07 Running command which aws
/root/.pyenv/shims/aws
```
