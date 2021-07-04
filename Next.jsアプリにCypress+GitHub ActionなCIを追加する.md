## はじめに

[前回](https://qiita.com/eyuta/items/a2454719c2d82c8bacd5)、Next.js アプリに Cypress を追加しました。
今回は、[Next.js](https://nextjs.org/) アプリに、 [Cypress](https://www.cypress.io/) と [GitHub Actions](https://github.com/features/actions) を使用した CI を組み込んでみました。

## 環境

- [Next.js](https://nextjs.org/)
  - v11.0.0
- [Cypress](https://www.cypress.io/)
  - v7.5.0
- [cypress-io/github-action@v2](https://github.com/cypress-io/github-action#start)
  - Cypress 公式の GitHub Actions
  - 自前で cypress を実行するステップを組むより、簡単に設定可能
  - マイナーバージョンを省略した場合、自動で最新版 (7/4 時点では v2.11.5) が適用される

## ソースコード

<https://github.com/eyuta/cypress-nextjs-test/tree/github-actions>

## 手順

### 1. 前提

※[こちら](https://qiita.com/eyuta/items/a2454719c2d82c8bacd5#3-getting-started)で作成したアプリをそのまま使ってます。

<details><summary>ざっくり手順
</summary><div>

### インストール

```sh
# Next.jsインストール
yarn create next-app --typescript # or npx create-next-app --ts

cd my-app

# 依存関係を分離するため、直下にe2eフォルダを作成し、そこにCypressをインストールする
mkdir e2e
cd e2e
echo e2e/node_modules >> .gitignore
yarn init -y # or npm init -y
yarn add -D cypress typescript # or npm install -D cypress typescript
npx tsc --init --types cypress --lib dom,es6
```

### 実行

```sh
# /e2e/cypress, /e2e/cypress.json が作成される
# ブラウザ起動後、Ctrl+Cで停止して良い
npx cypress open
```

### Next.js アプリの検証用テストケース追加

```sh
echo "
context('Next.js test', () => {
  it('should access localhost', () => {
    cy.visit('http://localhost:3000');
    cy.get('h1')
      .should('have.text', 'Welcome to Next.js!')
  });
});
" > e2e/cypress/sample.spec.ts
```

</div></details>

### 2. ワークフローの追加

※ワークフローの詳細については、[公式 Docs](https://docs.github.com/ja/actions/learn-github-actions)を参照してください。

```shell
# .github/workflows/e2e.yml を作成
mkdir -p .github/workflows
touch .github/workflows/e2e.yml
```

以下のように `.github/workflows/e2e.yml` を修正します。

```yaml
# 任意のワークフロー名
name: End-to-end tests
# トリガーするイベントを設定。この場合はpush
# 参考: https://docs.github.com/ja/actions/reference/events-that-trigger-workflows#example-workflow-configuration
on: [push]
jobs:
  # 任意のジョブ名を指定
  cypress-run:
    # 使用するランナーを指定。
    # Cypress公式が20.04を指定しているので合わせているが、どのバージョンでも問題ないはず
    runs-on: ubuntu-20.04
    steps:
      # リポジトリをチェックアウトし、ワークフローがリポジトリにアクセスできるようにする
      # 参考: https://github.com/actions/checkout
      - name: Checkout
        uses: actions/checkout@v2

      # Next.jsアプリを起動
      - name: Start App
        run: |
          yarn
          yarn build
          yarn start &

      # cypress-io/github-action@v2 を使用してCypressを実行
      - uses: cypress-io/github-action@v2
        with:
          # Next.js (http://localhost:3000) の起動を待つ
          wait-on: "http://localhost:3000"
          # Cypress を ./e2e/ に配置しているため、working-directoryを指定する
          # デフォルトはルートディレクトリ
          # 参考: https://github.com/cypress-io/github-action#working-directory
          working-directory: e2e

      # 任意
      # Cypress実行時に生成されたビデオとスクリーンショットを保存できる
      # 参考: https://github.com/cypress-io/github-action#artifacts
      - uses: actions/upload-artifact@v2
        if: always()
        with:
          name: Cypress E2E Videos
          path: e2e/cypress/videos
```

<details><summary>コメントなしバージョン
</summary><div>

```yaml
name: End-to-end tests
on: [push]
jobs:
  cypress-run:
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Start App
        run: |
          yarn
          yarn build
          yarn start &

      - uses: cypress-io/github-action@v2
        with:
          wait-on: "http://localhost:3000"
          working-directory: e2e

      - uses: actions/upload-artifact@v2
        if: always()
        with:
          name: Cypress E2E Videos
          path: e2e/cypress/videos
```

</div></details>

### 3. 変更を Push

変更をリポジトリに Push すると、GitHub Actions が自動で実行されます。
以下の手順で詳細を確認できます。

1. Your repository > Actions
   ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/82a36413-4260-1397-89d1-52979fae1f49.png)
2. 確認したいワークフロー > `cypress-run` > `Run cypress-io/github-action@v2`

また、`確認したいワークフロー > Artifacts`に、実行時の mp4 が格納されているため、実行時の動作も確認できます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/a03935fb-b0fd-626c-a1db-bd3e9249b7c5.png)

## おわりに

今回は、簡単に Cypress を GitHub Actions で実行するところまで行いました。
[公式 Docs](https://github.com/cypress-io/github-action)を見る限り様々なワークフローに対応できそうなので、折を見て色々試してみます。

## トラブルシューティング

### `AssertionError: expected { Object (baseUrl, projectRoot, ...) } to have property 'baseUrl' of null, but got 'http://localhost:3000'`

`e2e/cypress.json`に`baseUrl`が指定してあると、サンプルの`e2e/cypress/integration/examples/cypress_api.spec.js:174:30`が転びます。
`baseUrl`を削除するか、当該テストケースを削除してください。

## 参考

- [GitHub Actions について学ぶ - GitHub Docs](https://docs.github.com/ja/actions/learn-github-actions)
- [JavaScript End to End Testing Framework | cypress.io](https://www.cypress.io/)
- [cypress-io/github-action: GitHub Action for running Cypress end-to-end tests](https://github.com/cypress-io/github-action#start)
- [Writing E2E tests for a Next.js app with Cypress](https://getstarted.sh/bulletproof-next/e2e-testing-with-cypress/9)
