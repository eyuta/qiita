## はじめに

Amplify Console に Deploy されたアプリを、Amplify Console の Auto-build ではなく、CodeBuild から build を実行する方法です。

## 経緯

2020 年 7 月現在、Amplify Console の build process は、CodePipeline のようにカスタマイズすることができません。

([issue](https://github.com/aws-amplify/amplify-console/issues/103)としては挙がっているようです)

そのため、承認フローを挟む場合は自前の CodeBuild で [aws-amplify-cli](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/amplify/index.html#cli-aws-amplify)を使うか、webhooks を使う形になるかと思います。

本記事では、aws-amplify-cli を使って Amplify を既存の CodeBuild 内で実行する方法について解説します。

## 前提

Amplify, Amplify Console, Codebuild, CodePipeline の使い方については説明しません。
それらのリソースがすでに存在している前提で進めます。

また、Amplify には既に Repository が紐付けられており、 Auto-build が走る状態になっているとします。

## 設定方法

1. Auto-build を無効化する
   - App settings > General > Action > Disable auto build.
   - 参照: [disable automatic builds](https://docs.aws.amazon.com/amplify/latest/userguide/build-settings.html#disable-automatic-builds)
2. CodeBuild の `buildspec.yml` に、aws-amplify-cli を追加する
   - 参照: [start-job](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/amplify/start-job.html)
   - `$YOUR_APP_ID` は適宜設定する (Amplify App の ARN の apps の後ろの文字列)

```yml:buildspec.yml
version: 0.2

phases:
  build:
    commands:
      - |
        aws amplify start-job \
          --app-id $YOUR_APP_ID \
          --branch-name develop \
          --job-type RELEASE
```

## 最後に

割合かんたんに設定することができました。
ただ、Amplify CLI とは別に aws-amplify-cli があることを知らなかったため、調べるのに時間がかかりました。
Amplify CLI では実現できなかった Amplify Console への Deploy も、aws-amplify-cli でできそうですね......!!
