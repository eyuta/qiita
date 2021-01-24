# VSCode を使って社内 Gitlab の Issue・MR を操作する

## はじめに

gitlab 謹製の VSCode の拡張機能 [GitLab Workflow](https://marketplace.visualstudio.com/items?itemName=GitLab.gitlab-workflow) が便利だったので、紹介します。

この拡張を使うと、VSCode と Gitlab がシームレスに繋がります。

## 機能紹介

この拡張機能では、以下のようなことが可能です。

※こちらに挙げたのは一部です。詳細は[こちら](https://marketplace.visualstudio.com/items?itemName=GitLab.gitlab-workflow)。

- VSCode で、自分にアサインされている Issue や MR の一覧や詳細を閲覧できる
    - MR は、description とファイル差分もそれぞれ閲覧可能
    - デフォルトのビューは以下の 5 つだが、表示したい Issue や MR は[カスタム可能](https://marketplace.visualstudio.com/items?itemName=GitLab.gitlab-workflow#custom-queries)
      - `Issues assigned to me`: assignee が自分の Issue
      - `Issues created by me`: author が自分の Issue
      - `Merge Requests assigned to me` : assignee が自分の MR
      - `Merge Requests created by me` : author が自分の MR
      - `All project merge requests` : すべての MR
    - 特に、差分を VSCode の differ で見れるのはありがたい
    ![sidebar.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/90eb51eb-1ab4-1f3c-74f9-1708ef65e590.png)
- VScode から、Gitlab の[Advanced Search](https://docs.gitlab.com/ee/user/search/advanced_global_search.html)を行うことができる
    - Issue, MR, Commit, Wiki 等の横断検索が可能
    - [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)では Commit の検索は可能なので、使い分け
- VSCode で、Issue や MR の詳細・編集が可能
    - GUI 上はコメントの追加しかできないが、[GitLab Quick Actions](https://docs.gitlab.com/ee/user/project/quick_actions.html)が使用可能なので、大抵のことはできる。以下、一部抜粋
      - `/assign me` : assignee を自分に
      - `/assign @user_name` : assignee を @user_name に
      - `/unassign` : assignee を空に
      - `/create_merge_request <branch name>` : MR 作成
      - `/due <date>` : due date の指定
      - `/label ~label1 ~label2` : ラベル追加
    - ![issue.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/0a045a2e-4263-f9a9-feda-c126f991501a.png)
- Snippet の作成・挿入が可能
- GitLabCI 構成の検証が可能
    - 詳しくは見てないが、[Validate the CI YAML configuration](https://docs.gitlab.com/ee/api/lint.html#validate-the-ci-yaml-configuration)に相当することができそう

## 環境

```txt
Windows 10: version 2004
Gitlab: GitLab Community Edition 13.6.1
GitLab Workflow: version3.9.0
VSCode: 1.52.1
```

## 設定手順

参照: [setup](https://marketplace.visualstudio.com/items?itemName=GitLab.gitlab-workflow#setup)

### Step1: PAT(Personal Access Token)の発行

1. <https://your-gitlab-domain/profile/personal_access_tokens>を開く
     - 或いは `Settings`> `Access Tokens`
2. `Add a personal access token` の必要項目を埋める
     - `Name`: Token の名前
     - `Expires at`: Token の期限
     - `Scopes`: `api`, `read_user`を選択
     ![add_personal_access_token.PNG](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/fc9cc7a0-7eca-07be-455a-1da7a6f5fa65.png)

3. `Create personal access token`をクリック
4. 発行された画面上部の Token をコピー
   ![token.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/98363b94-05b9-330b-4817-ea18f4509789.png)

### Step2: Gitlab Workflow に Token と Domain を設定する

1. VSCode を開く
2. `Ctrl + Shift + P`で Command Palette を開く(Mac の場合は`Cmd + Shift + P`)
3. `GitLab: Set GitLab Personal Access Token`を検索してクリック
   ![set_gitlab_access_token.PNG](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/f05ed6cd-0882-3f08-d590-b97f45726d5f.png)

4. `your-gitlab-domain` > `作成したToken`の順に入力
     - ドメインを入力する際は、末尾に`/`を入れないようにする(入れるとエラー)
5. [カスタムドメインを使用している場合のみ]
   1. `Settings.json`を開く
   2. `"gitlab.instanceUrl": "http://your-gitlab-domain",`を追加
        - こちらも末尾に`/`を入れないようにする
        - 詳細は[こちら](https://marketplace.visualstudio.com/items?itemName=GitLab.gitlab-workflow#configuration-options)

### Step3: 実際に使用する

1. `Ctrl + Shift + P`で Command Palette を開く(Mac の場合は`Cmd + Shift + P`)
2. `View: Show Gitlab Workflow` を実行
3. 現在の Workspace に設定されたリモート URL に存在する Issue と MR が、 View に表示される
     - デフォルトでは RemoteName が`origin`の URL を使用する。変更したい場合は`gitlab.remoteName`を設定する

## 他にできること

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/a8f21860-8f47-7404-5801-77a1a90dc22e.png)

## 終わりに

Gitlab Workflow を使うことで、VSCode とブラウザを行き来する機会が減り、より開発に専念できるようになりました。

## 参考文献

- [GitLab Workflow](https://marketplace.visualstudio.com/items?itemName=GitLab.gitlab-workflow)
- [Advanced Search Syntax](https://docs.gitlab.com/ee/user/search/advanced_search_syntax.html)
- [GitLab Quick Actions](https://docs.gitlab.com/ee/user/project/quick_actions.html)
