# 既存の Amplify App をモノレポに対応させる

この記事は[AWS Amplify Advent Calendar 2020](https://qiita.com/advent-calendar/2020/amplify) 4 日目の記事となります。

## はじめに

今年はじめより、[Amplify Console がモノレポに対応](https://aws.amazon.com/jp/about-aws/whats-new/2020/06/amplify-console-supports-deploying-and-hosting-web-apps-managed-in-monorepos/)しました。
モノレポ対応について公式のドキュメントはありませんが、[こちらのブログ記事](https://aws.amazon.com/jp/blogs/mobile/set-up-continuous-deployment-and-hosting-for-a-monorepo-with-aws-amplify-console/)に作成方法について記述があります。

ただ、このブログ記事には、既存の`Amplify App`をモノレポ化する方法について触れられていません。
(この記事では、Amplify Console 上で作成したアプリのことを、`Amplify App`と呼ぶことにします)
(アプリを作り直せばいい話かもしれませんが、Ci/CD の関係上別アプリを作成するのは高コストでした)

また、こちらの[build-settings](https://docs.aws.amazon.com/amplify/latest/userguide/build-settings.html)で一部モノレポについて触れられてはいますが、内容が不十分だったり、間違っていたりしています。
(2020/12/04 時点。修正依頼は出したので、そのうち直るかもしれません)

今回そのあたりを設定する機会があったので、備忘を兼ねて残しておきます。

## 前提

1. 以下のようなリポジトリがあるものとする

   ```tree
   repo
   ├── app1
   └── app2

   ```

2. 上記の App1 について、既に`Amplify App`が存在するものとする
3. 2.の`Amplify App`の`build settings`の`App build specification`が以下のようになっている

   ```yml
   version: 1
   frontend:
     phases:
       preBuild:
         commands:
           - cd app1
           - npm ci
       build:
         commands:
           - npm run build
     artifacts:
       baseDirectory: app1/build
       files:
         - "**/*"
     cache:
       paths: []
   ```

## 対応方法

※amplify.yml を使用しているか否か、で対応が少し変わります。

### amplify.yml を使用していない場合

- `applications`でモノレポであることを明記
- `appRoot`でビルドするディレクトリを指定

具体的には、`App build specification`が以下のようになります。

```yml
version: 1
applications:
  - frontend:
    phases:
      preBuild:
        commands:
          - npm ci
      build:
        commands:
          - npm run build
    artifacts:
      baseDirectory: /
      files:
        - "**/*"
    cache:
      paths: []
    appRoot: app1
```

### amplify.yml を使用している場合

amplify.yml はルートディレクトリに設置する必要があります。
そのため、app1 と app2、2 つのプロジェクトのビルド手順を amplify.yml に記入する必要があります。

```yml
version: 1
applications:
  - frontend:
    phases:
      preBuild:
        commands:
          - npm ci
      build:
        commands:
          - npm run build
    artifacts:
      baseDirectory: /
      files:
        - "**/*"
    cache:
      paths: []
    appRoot: app1
  - frontend:
    phases:
      preBuild:
        commands:
          - npm ci
      build:
        commands:
          - npm run build
    artifacts:
      baseDirectory: /
      files:
        - "**/*"
    cache:
      paths: []
    appRoot: app2
```

次に、各`Amplify App`の`App build specification`に以下を設定します。
これにより、それぞれの`Amplify App`が app1, app2、どちらに対応しているか AWS が判断することができます。

```yml
version: 0.1
applications:
  - appRoot: app1
```

```yml
version: 0.1
applications:
  - appRoot: app2
```

## 注意点

### スラッシュは必要ない

[こちらのドキュメント](https://docs.aws.amazon.com/amplify/latest/userguide/build-settings.html)には`appRoot: /react-app`のようにディレクトリ名の先頭にスラッシュをつけています。

同じようにスラッシュをつけて実装しても満足に動作しなかったので AWS に問い合わせたところ、スラッシュをつけると正しく認識されない、とのことです。
よって、`appRootに指定するディレクトリの先頭にはスラッシュをつけない`のが正しいです。
(ドキュメントもそのうち修正されるとのこと)

### `App build specification` が常に`amplify.yml`で上書きされるわけではない

今まで、amplify.yml が存在する場合は``App build specification``に設定された値が上書きされるものとばかり思っていました。
モノレポではない場合それは正しいですが、モノレポの場合は上に示したように両者を適切に設定しないと思うように動作しません。
