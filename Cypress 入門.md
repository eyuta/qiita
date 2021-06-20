## はじめに

Cypress の勉強のため、[公式ドキュメント](https://docs.cypress.io/guides/overview/why-cypress)の概要～入門を簡単にまとめました。
皆様の参考になれば幸いです。

## この記事で分かること

- Cypress の概要
- Selenium との比較
- 入門 (サンプルアプリの作成)
  - Next.js + TypeScript + Cypress の簡単なサンプルアプリの作成

## 環境

```json
{
  "cypress": "^7.5.0",
  "typescript": "^4.3.4"
}
```

## 1. Cypress とは

Github: [cypress-io/cypress: Fast, easy and reliable testing for anything that runs in a browser.](https://github.com/cypress-io/cypress)
公式 Doc: [Why Cypress? | Cypress Documentation](https://docs.cypress.io/guides/overview/why-cypress)

Cypress はフロントエンドのテストツールであり、以下のすべてのを作成・テストできる。

- エンドツーエンド(E2E)テスト
- 統合(インテグレーション)テスト
- ユニットテスト

### 1.1. Cypress のエコシステム (Cypress ecosystem)

Cypress はローカルにインストールされるテストランナーと、テスト記録用のダッシュボードで構成される。

- 開発中: ローカルでアプリケーションを開発中、常にテストを回すことができる (=TDD に最適！)
- 開発後: テストを構築し、Cypress を CI ツールと統合した後、ダッシュボードでテストの実行結果を記録・確認できる

### 1.2. 特徴(Features)

- **タイムトラベル**:
  - Cypress はテスト実行時にスナップショットを取得できる
  - [コマンドログ](https://docs.cypress.io/guides/core-concepts/test-runner#Command-Log)にカーソルを合わせると、各ステップの詳細を確認できる
- **デバッグ可能**:
  - テストの失敗理由を推測せず、開発ツールから[直接デバッグ](https://docs.cypress.io/guides/guides/debugging)できる
  - そのため、読み込み可能なエラーとスタックトレースにより、デバッグが非常に高速になる
- **自動待機**:
  - テストに `await` や `sleep` はいらない
  - Cypress は、コマンドやアサーション時に要素が見つからない場合、[自動で再試行](https://docs.cypress.io/guides/core-concepts/introduction-to-cypress#Cypress-is-Not-Like-jQuery)する
- **[Stubs, Spies, Clocks](https://docs.cypress.io/guides/guides/stubs-spies-and-clocks)**:
  - [sinon](https://sinonjs.org/)の Spy や Stub をラップしている
  - [Clocks(Date、setTimeout、clearTimeout、setInterval, clearInterval)](https://docs.cypress.io/api/commands/clock)のラッパーも提供している
- **ネットワークトラフィック制御**
  - サーバーを使用せずに、[ネットワークリクエスト](https://docs.cypress.io/guides/guides/network-requests#Use-Server-Responses)を制御、Stub, テストできる
- **一貫した結果**
  - Selenium や WebDriver を使用していないため、高速で一貫性のある信頼性の高いテストを提供する
- **スクリーンショットとビデオ**
  - 失敗時に、以下を表示できる
    - 自動的に撮影されたスクリーンショット
    - CLI から実行した場合のテストスイート全体のビデオ
- **[クロスブラウザテスト](https://docs.cypress.io/guides/guides/cross-browser-testing)**
  - CI パイプライン上で、Firefox および Chrome 系のブラウザを用いて最適なテストを実行できる

### 1.3. サンプル

- [公式動画](https://docs.cypress.io/guides/overview/why-cypress#Setting-up-tests): Cypress をインストールしてから実際にテストケースを蹴るところまで、簡潔にまとまっている
- [cypress-io/cypress-realworld-app](https://github.com/cypress-io/cypress-realworld-app): Cypress のテスト手法やシナリオを実践するためのアプリケーション

### 1.4. テストタイプ

#### E2E テスト

一般的な E2E テストのように、ブラウザでアプリケーションにアクセスし、実際のユーザーと同じように UI を介してアクションを実行する。

> ```js
> it("adds todos", () => {
>   cy.visit("https://todo.app.com");
>   cy.get(".new-input").type("write code{enter}").type("write tests{enter}");
>   // confirm the application is showing two items
>   cy.get("li.todo").should("have.length", 2);
> });
> ```

#### コンポーネントテスト

一部の Web フレームワーク (Rect or Vue) からコンポーネントをマウントすることで、コンポーネントテストも実行できる。
jest や mocha のようにコンポーネントを jsdom でレンダリングするのではなく、実際のブラウザ上にコンポーネントをマウントし、そのコンポーネントに対してテストを行う。

**※ただし、現状(2021/06/19)段階でアルファ版なので注意**

> ```js
> import { mount } from "@cypress/react"; // or @cypress/vue
> import TodoList from "./components/TodoList";
>
> it("contains the correct number of todos", () => {
>   const todos = [
>     { text: "Buy milk", id: 1 },
>     { text: "Learn Component Testing", id: 2 },
>   ];
>
>   mount(<TodoList todos={todos} />);
>   // the component starts running like a mini web app
>   cy.get("[data-testid=todos]").should("have.length", todos.length);
> });
> ```

#### API テスト

Cypress は任意の HTTP 呼び出しを実行できるため、API テストも可能。

> ```js
> it("adds a todo", () => {
>   cy.request({
>     url: "/todos",
>     method: "POST",
>     body: {
>       title: "Write REST API",
>     },
>   })
>     .its("body")
>     .should("deep.contain", {
>       title: "Write REST API",
>       completed: false,
>     });
> });
> ```

#### その他

[多数のプラグイン]](<https://docs.cypress.io/plugins/directory>)を活用することで、[a11y](https://github.com/component-driven/cypress-axe)、[画像回帰](https://docs.cypress.io/plugins/directory#Visual%20Testing)、[電子メール](https://docs.cypress.io/faq/questions/using-cypress-faq#How-do-I-check-that-an-email-was-sent-out)テスト等々が可能。

## 2. [Selenium との比較](https://docs.cypress.io/guides/overview/key-differences#Architecture)

### 2.1. アーキテクチャ

- Selenium は、ブラウザの外部で実行され、ネットワーク経由でコマンドを実行する
- Cypress は、ブラウザ内で、アプリケーションと同じランタイムで実行される

#### Cypress の特徴

- アプリケーションのイベントにリアルタイムでアクセス可能
- ネットワークトラフィックをその場で読み取り、変更できるため、ネットワーク層でも動作する
  - それにより、ブラウザに出入りするすべてを変更可能
  - ブラウザの自動化を妨げるコードを変更できる
- Cypress は自動化プロセス全体を制御する
  - それによりブラウザの内外で発生するすべてを理解できる
  - それにより、Cypress はどのテストツールよりも一貫した結果を提供できる
- Cypress はローカルマシンにインストールされる
  - 自動化タスクのために、OS の機能を追加利用できる
  - [スクリーンショットの撮影、ビデオの録画](https://docs.cypress.io/guides/guides/screenshots-and-videos)、[ファイル操作](https://docs.cypress.io/api/commands/exec)、[ネットワーク操作](https://docs.cypress.io/api/commands/request)、等々

### 2.2. ホストオブジェクトへのネイティブアクセス

Cypress はアプリケーション内で動作するため、全てのホストオブジェクト(`window`, `document`, `DOM element`, `Application Instance`, `function`, `timer`, `service worker`, etc)へのネイティブアクセスを提供する。
つまり、テストコードにおいて、アプリケーションコードと同じオブジェクトにアクセス可能。

### 2.3. 新しい種類のテスト

上記の特徴により、従来では時間と費用のかかった以下のようなケースを人為的に作成可能。

- ブラウザ or アプリケーションの機能をスタブし、テストケースで必要に応じて動作するように強制する
- Redux のようにデータストアを公開し、テストコードから直接アプリケーションの状態を変更する
- サーバーから空のレスポンスを返すことで、「空のビュー」などのエッジケースをテストする
- [レスポンスのステータスコードを 500 に変更する](https://docs.cypress.io/api/commands/route)ことで、アプリケーションの異常系のテストが可能
- DOM 要素を直接変更(Ex. 非表示 DOM を表示するように)
- プログラムでサードパーティのプラグインを利用可能
  - 以下のような複雑な UI ウィジェットの代わりに、テストコードから直接メソッドを呼び出し、制御が可能
  - Ex. 複数選択、オートコンプリート、ドロップダウン、ツリービュー、カレンダー
- テスト時、アプリケーションコードが実行される前に、[Google Analytics が読み込まれないようにする](https://docs.cypress.io/guides/references/configuration#blockHosts)
- アプリケーションが新しいページに移行したり、アンロードされるたびに同期通知を受け取る
- テストで必要な時間を待たずにタイマー or ポーリングが自動的に起動するように、時間を前後に動かす
- アプリケーションに応答する独自のイベントリスナーを追加する
  - テスト中に異なる動作をするようにアプリケーションコードを更新できる
  - テストコードから WebSocket メッセージを制御する
  - サードパーティのスクリプトを条件付きでロードする
  - アプリケーションで直接関数を呼び出す

### 2.4. ショートカット

Cypress は、特定の状況を生成するために「ユーザーのように振る舞う」ことを矯正しない。
Cypress では、プログラムからアプリケーションを操作及び制御できる。

- 例
  - 他のツール
    1. ログインページにアクセス
    2. ユーザー名とパスワードを入力
    3. 送信
    4. リダイレクト後、テストを実施 (これをテストケース毎に実行)
  - Cypress
    - [cy.request()](https://docs.cypress.io/api/commands/request)で直接同期的にログインリクエストを送信し、テストを行う

`cy.request()`を利用した場合、リクエストがブラウザから送信されたかのように Cookie を自動的に取得・設定する。また、CORS も完全にパイバスされる。

### 2.5. 取りこぼしのないテスト (Flake resistant)

- Cypress はアプリケーションで同期的に発生するすべてを理解している
  - ページロード、アンロードのタイミングで通知される
  - イベント発生時、要素の変更を見逃さない
  - [要素がアニメーション中か自動で判定し、停止するまで待機する](https://docs.cypress.io/guides/core-concepts/interacting-with-elements#Animations)
  - さらに、[要素がちゃんと表示され](https://docs.cypress.io/guides/core-concepts/interacting-with-elements#Visibility)、[有効(`disabled=false`)になり](https://docs.cypress.io/guides/core-concepts/interacting-with-elements#Disability)、[親要素にカバーされなくなる](https://docs.cypress.io/guides/core-concepts/interacting-with-elements#Covering)まで待機する
  - ページ遷移が始まると、次ページが完全に読み込まれるまで待機する
  - 特定のネットワークリクエストが終了するのを[待つ](https://docs.cypress.io/api/commands/wait)ように指示もできる

### 2.6. デバッグ可能

Cypress は使いやすさを大切にしている。

- テストに失敗すると、失敗した正確な理由を示す数百のエラーメッセージが出力される
- 以下の要素を視覚的に表現する豊富な UI がある
  - コマンドの実行、アサーション、ネットワークリクエスト、スパイ、スタブ、ページロード、URL の変更
- アプリケーションのスナップショットにより、コマンドが実行された状態にタイムトラベルできる
- テスト実行中に開発者ツールが使える
  - すべてのコンソールメッセージ、ネットワークリクエストを確認できる
  - 要素の検査ができる
  - テストコードやアプリケーションコードでデバッガーステートメントを使用できる

これらにより、開発とテストのすべてを蔵時に行うことができる。

### 2.7. トレードオフ

これらの機能を可能にするに行った[トレードオフ](https://docs.cypress.io/guides/references/trade-offs#Permanent-trade-offs-1)がある。

- [テストコードはあくまでクライアントサイドで評価される](https://docs.cypress.io/guides/references/trade-offs#Inside-the-browser)。**サーバーサイド(Node.js)では評価されない**
- [複数タブ、複数ブラウザをサポートしていない](https://docs.cypress.io/guides/references/trade-offs#Multiple-tabs)
- [Same Origin には対応しているが、Cross Origin には対応していない](https://docs.cypress.io/guides/references/trade-offs#Same-origin)

## 3. Getting Started

簡単な Next.js (+ Typescript) アプリを起動し、テストを作成・実行してみる。
※ Next.js の部分は任意のフレームワークに置き換えて良いです。
※ 不要であれば読み飛ばしてください。

### 3.1. 環境

OS 別のセットアップ方法は[こちら](https://docs.cypress.io/guides/getting-started/installing-cypress#System-requirements)に詳細が記載されているので、適宜確認する。

```sh
$ node -v
v16.3.0

$ npm -v
7.15.1
```

### 3.2. ソース

<https://github.com/eyuta/cypress-nextjs-test>

### 3.3. 準備

#### 3.3.1. インストール

```sh
# Next.jsインストール。他でも可。既存プロジェクトでも可。
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

#### 3.3.2. Cypress を開く

[`npx cypress open`, `yarn run cypress open`](https://docs.cypress.io/guides/getting-started/installing-cypress#Opening-Cypress)で実行可能。

`package.json`を使う場合は、 scripts に以下を追加する。

```json
{
  "scripts": {
    "cypress:open": "cypress open",
    "cypress:run": "cypress run"
  }
}
```

```sh
yarn cypress:open
```

これにより、以下が行われる。

- テストランナー用のブラウザが起動される
- `/e2e/cypress`フォルダが作られ、配下にサンプルテストケースが配置される
- `/e2e/cypress.json`が作成される

テストランナー起動後、`Tests`の適当なファイル名をクリックすると、テストが実行されます。

CLI ツールの詳細は[こちら](https://docs.cypress.io/guides/guides/command-line#Installation)
ダッシュボードの使い方は[こちら](https://docs.cypress.io/guides/dashboard/introduction)

### 3.4. 新しいテストケースの追加

新しいテストケースを追加する。
その前に、Next.js を使用している場合は、一旦サーバーを別のターミナルで起動しておく。

```sh
yarn dev
```

```sh
echo "
context('Next.js test', () => {
  it('should access localhost', () => {
    cy.visit('http://localhost:3000');
    cy.get('h1')
      .should('have.text', 'Welcome to Next.js!')
  });
});
" > e2e/cypress/integration/sample.spec.ts
```

Next.js を使用していない場合は、適当なサイトにアクセスするテストケースを作成する (Cypress が[サンプルページ](https://example.cypress.io)を提供している)。

```sh
echo "
context('google search', () => {
  it('should perform basic google search', () => {
    cy.visit('https://google.com');
    cy.get('[name="q"]')
      .type('subscribe')
      .type('{enter}');
  });
});
" > e2e/cypress/integration/sample.spec.ts

```

`yarn cypress:open`で起動したブラウザにおいて、`Tests`に`sample.spec.ts`が追加される。
そして、`sample.spec.ts`をクリックすると、作成したテストケースが実行される。

### 3.5. 成功/失敗するテストケースの追加

先程の`sample.spec.ts`もしくは別のファイルに、以下を追記し、実行する。
すると、成功した場合はチェックが付き、失敗した場合にエラーが表示されることが分かる。

基本的なページアクセス、クエリ、イベント発火方法等々については、[こちら](https://docs.cypress.io/guides/getting-started/writing-your-first-test#Write-a-real-test)を見るか、サンプルテストを見ると分かりやすい。
エラーの見方は、[こちら](https://docs.cypress.io/guides/guides/debugging#Errors)に詳細がある。

```js
describe("My First Test", () => {
  it("Match!", () => {
    expect(true).to.equal(true);
  });
  it("Does not match!", () => {
    expect(true).to.equal(false);
  });
});
```

### 3.6. アプリのテスト

同じアプリケーション(ホスト)に頻繁にアクセスする場合、`baseUrl`を指定すると良い。
それにより、`cy.visit()` と `cy.request()` に自動的にプレフィックスが付与されるため、ホスト名とポートを省略できる。

```cypress.json
{
  "baseUrl": "http://localhost:3000"
}
```

```sample.spec.ts
describe('The Home Page', () => {
  it('successfully loads', () => {
    cy.visit('/')
  })
})
```

### 3.7. テスト戦略

#### 3.7.1. [初期データの用意](https://docs.cypress.io/guides/getting-started/testing-your-app#Seeding-data)

テスト前にサーバーサイド(データベース等)でセットアップや破棄を行いたい場合、`beforeEach`や`afterEach`を使う。
また、以下のメソッドも有用。

##### [cy.exec()](https://docs.cypress.io/api/commands/exec)

システムコマンドを実行する。

> ```js
> describe("The Home Page", () => {
>   beforeEach(() => {
>     // reset and seed the database prior to every test
>     cy.exec("npm run db:reset && npm run db:seed");
>   });
>
>   it("successfully loads", () => {
>     cy.visit("/");
>   });
> });
> ```

##### [cy.task()](https://docs.cypress.io/api/commands/task#Syntax)

[pluginsFile](https://docs.cypress.io/guides/references/configuration#Folders-Files) を介して Node でコードを実行する。

例 1: ログの出力

> ```spec.js
> cy.task("hello", { greeting: "Hello", name: "World" });
> ```
>
> ```plugins/index.js
> module.exports = (on, config) => {
>   on("task", {
>     // deconstruct the individual properties
>     hello({ greeting, name }) {
>       console.log("%s, %s", greeting, name);
>
>       return null;
>     },
>   });
> };
> ```

例 2: TODO アプリで、TODO が追加された後、それが本当に DB に保存されているかテストする

> ```spec.js
> import { enterTodo, resetDatabase } from './utils'
>
> describe('cy.task', () => {
>   beforeEach(resetDatabase)
>   beforeEach(() => {
>     cy.visit('/')
>   })
>
>   it('finds record in the database', () => {
>     // random text to avoid confusion
>     const id = Cypress._.random(1, 1e6)
>     const title = `todo ${id}`
>     enterTodo(title)
>     // confirm the new item has been saved
>     // https://on.cypress.io/task
>     cy.task('hasSavedRecord', title).should('equal', true)
>   })
> })
> ```
>
> ```cypress/plugins/index.js
> const fs = require('fs')
> const path = require('path')
> const repoRoot = path.join(__dirname, '..', '..')
>
> const findRecord = title => {
>   const dbFilename = path.join(repoRoot, 'data.json')
>   const contents = JSON.parse(fs.readFileSync(dbFilename, 'utf8'))
>   const todos = contents.todos
>   return todos.find(record => record.title === title)
> }
>
> module.exports = (on, config) => {
>   // "cy.task" can be used from specs to "jump" into Node environment
>   // and doing anything you might want. For example, checking "data.json" file!
>   on('task', {
>     hasSavedRecord (title) {
>       console.log('looking for title "%s" in the database', title)
>       return Boolean(findRecord(title))
>     }
>   })
> }
> ```

参考: [Incredibly Powerful cy.task | Better world by better software](https://glebbahmutov.com/blog/powerful-cy-task/)

#### 3.7.2. サーバーのスタブ

[cy.request()](https://docs.cypress.io/api/commands/request)で HTTP リクエストを行う際、事前に[cy.intercept()](https://docs.cypress.io/api/commands/intercept)を実行しておくことで、HTTP 通信をスタブできる。

> ```js
> cy.intercept(
>   {
>     method: "GET", // Route all GET requests
>     url: "/users/*", // that have a URL that matches '/users/*'
>   },
>   [] // and force the response to be: []
> );
> ```

参考: [Network Requests | Cypress Documentation](https://docs.cypress.io/guides/guides/network-requests#Routing)

#### 3.7.3. ログイン

ログイン機能のテストを行う場合は、以下のように UI を通してテストするのがおすすめ。

> ```js
> describe("The Login Page", () => {
>   beforeEach(() => {
>     // reset and seed the database prior to every test
>     cy.exec("npm run db:reset && npm run db:seed");
>
>     // seed a user in the DB that we can control from our tests
>     // assuming it generates a random password for us
>     cy.request("POST", "/test/seed/user", { username: "jane.lane" })
>       .its("body")
>       .as("currentUser");
>   });
>
>   it("sets auth cookie when logging in via form submission", function () {
>     // destructuring assignment of the this.currentUser object
>     const { username, password } = this.currentUser;
>
>     cy.visit("/login");
>
>     cy.get("input[name=username]").type(username);
>
>     // {enter} causes the form to submit
>     cy.get("input[name=password]").type(`${password}{enter}`);
>
>     // we should be redirected to /dashboard
>     cy.url().should("include", "/dashboard");
>
>     // our auth cookie should be present
>     cy.getCookie("your-session-cookie").should("exist");
>
>     // UI should reflect this user being logged in
>     cy.get("h1").should("contain", "jane.lane");
>   });
> });
> ```

しかし、各テストの前に毎回上記のログイン処理を行うのは冗長。
そこで、`/login`に直にログイン情報をポストすることで、ログイン処理を簡略化できる。

> ```js
> it("logs in programmatically without using the UI", function () {
>   // destructuring assignment of the this.currentUser object
>   const { username, password } = this.currentUser;
>
>   // programmatically log us in without needing the UI
>   cy.request("POST", "/login", {
>     username,
>     password,
>   });
>
>   // now that we're logged in, we can visit
>   // any kind of restricted route!
>   cy.visit("/dashboard");
>
>   // our auth cookie should be present
>   cy.getCookie("your-session-cookie").should("exist");
>
>   // UI should reflect this user being logged in
>   cy.get("h1").should("contain", "jane.lane");
> });
> ```

#### その他

[こちらの Best Practices](https://docs.cypress.io/guides/references/best-practices)も参考になる。

## 参考

- [Why Cypress? | Cypress Documentation](https://docs.cypress.io/)
- [Incredibly Powerful cy.task | Better world by better software](https://glebbahmutov.com/blog/powerful-cy-task/)
- [Cypress - TypeScript Deep Dive](https://basarat.gitbook.io/typescript/intro-1/cypress)
- [Basic Features: TypeScript | Next.js](https://nextjs.org/docs/basic-features/typescript)
