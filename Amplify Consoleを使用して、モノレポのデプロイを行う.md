# Amplify Console を使用して、モノレポ構成のプロジェクトのデプロイを行う

この記事は[AWS Amplify Advent Calendar 2020](https://qiita.com/advent-calendar/2020/amplify) 4 日目の記事となります。

## はじめに

今年はじめより、[Amplify Console がモノレポに対応](https://aws.amazon.com/jp/about-aws/whats-new/2020/06/amplify-console-supports-deploying-and-hosting-web-apps-managed-in-monorepos/)しました。
今回はこちらを使用し、モノレポのプロジェクトを Amplify Console にリリースしたいと思います。
公式のドキュメントはありませんが、[ブログ記事](https://aws.amazon.com/jp/blogs/mobile/set-up-continuous-deployment-and-hosting-for-a-monorepo-with-aws-amplify-console/)がありますので、基本これに沿って設定していく形になります。

## メリット

Amplify Console がモノレポ対応がされることにより、既存のプロジェクトに大きな変更を加えることなく、以下の機能を享受できるようになりました。

- モノリポジトリのサブフォルダを接続するときのビルド設定の自動検出。これにより、モノレポでのプロジェクトの接続と展開がスムーズになります。
- 選択的ビルドトリガー：Amplify Console の新しいビルドは、特定のアプリプロジェクト内でコードが変更された場合にのみトリガーされます。
- 単一のビルド仕様ファイル（amplify.yml）で複数のアプリのビルド設定を定義する機能。
