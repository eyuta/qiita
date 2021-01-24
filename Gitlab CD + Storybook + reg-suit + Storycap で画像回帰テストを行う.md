## はじめに

UI のデグレを防ぐため、Vue App に画像回帰テストを導入しました。

CI 環境は[Gitlab CI/CD](https://docs.gitlab.com/ee/ci/)を利用しました。

本記事は Gitlab CI/CD を用いた画像回帰テストの導入が主眼であり、Vue, Storybook の導入・作成については触れません。

## 構成

![gitlab (1).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/b99a9359-7bc6-9002-7ff4-8b9f3a54f609.png)

## 環境

GitLab: GitLab Community Edition 13.6.1

```package.json
{
  "vue": "^2.6.12",
  "@storybook/vue": "^5.3.19",

  "storycap": "^3.0.3",
  "puppeteer": "^5.5.0",

  "reg-suit": "^0.10.10",
  "reg-keygen-git-hash-plugin": "^0.10.11",
  "reg-notify-gitlab-plugin": "^0.10.11",
  "reg-notify-slack-plugin": "^0.10.10",
  "reg-publish-s3-plugin": "^0.10.10"
}
```

## 前提

前述したとおり、アプリ、Storybook は作成済みとします。

## 手順

1. Storycap のインストール
2. Storycap の設定
3. reg-suit のインストール
4. reg-suit の設定
5. Gitlab CI/CD の設定

### 1. Storycap のインストール

今回は Storybook からスクリーンショットを生成するためのツールとして、[Storycap](https://github.com/reg-viz/storycap) を採用しました。
こちらは、[storybook-chrome-screenshot](https://www.npmjs.com/package/storybook-chrome-screenshot) や [zisui](https://www.npmjs.com/package/zisui)と呼ばれるツールの後継となります。
[reg-suit](https://github.com/reg-viz/reg-suit)と製作者が同一です。

さて、[こちら](https://github.com/reg-viz/storycap)を参考に、Storycap をインストールします。

```sh
yarn add -D reg-suit storycap puppeteer
```

Chromium を制御するツールとして、Puppeteer も一緒にインストールしています。
[v3.0.0 以降、Storycap は Puppeteer を直接使用しません。](https://github.com/reg-viz/storycap#chromium-version)
そのため、Puppeteer を使用したい場合は明示的にインストールする必要があります。

### 2. Storycap の設定

[こちら](https://github.com/reg-viz/storycap#getting-started)を参考に、package.json に scripts を追加します。
[※ Storybook 起動時の timed out を避けるため、serverTimeout を追加しています。](#1-timed-out)

```package.json
{
  "scripts": {
    "storybook": "start-storybook -p 6006 -s public",
    "storycap": "storycap --serverCmd \"yarn storybook\" http://localhost:6006 --serverTimeout 600000"
  }
}
```

```.gitignore
__screenshots__
```

この状態で `yarn storycap` を行うと、デフォルトの `__screenshots__` ディレクトリにスクリーンショットが格納されます。

### 3. reg-suit の導入

インストール後、初期設定を行います。

```sh
yarn add -D reg-suit
# pluginのインストール時、yarnを使う場合は --use-yarn を指定する
yarn run reg-suit init --use-yarn
# Answer a few questions...
```

初期設定では、Plugin のインストールと設定ファイルの生成を行っているようです。
今回は以下の Plugin を選択しました。

- reg-keygen-git-hash-plugin
  - 各コミットのハッシュ値から、比較対象のコミットを識別するようです。デフォルトで選択済みになっているため、そのまま選択します
- reg-publish-s3-plugin
  - s3 に画像をアップロードするために使用します
- reg-notify-gitlab-plugin
  - gitlab 連携に使用します。今回のメインです

また、`Directory contains actual images` は storycap の出力先である`__screenshots__`を指定しました。

これにより、以下のようなファイルが生成されます。

```regconfig.json
{
  "core": {
    "workingDir": ".reg",
    "actualDir": "__screenshots__",
    "thresholdRate": 0,
    "ximgdiff": {
      "invocationType": "client"
    }
  },
  "plugins": {
    "reg-keygen-git-hash-plugin": true,
    "reg-notify-gitlab-plugin": {
      "projectId": "2139",
      "privateToken": "xxxxxxxxxxxxxxxx",
    },
    "reg-publish-s3-plugin": {
      "bucketName": "reg-publish-bucket-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
  }
}
```

package.json に [`reg-suit run`](https://github.com/reg-viz/reg-suit#run-command) コマンドを追加します。

```package.json
{
  "scripts": {
    "reg-suit": "reg-suit run",
  }
}
```

この状態で `yarn reg-suit` を行うと、現行の`__screenshots__`に存在する画像群がアップロードされます。

### 4. Gitlab CI/CD の設定

さて、本題です。

#### 4.1. projectId の取得

まずはじめに [projectId](https://github.com/reg-viz/reg-suit/blob/master/packages/reg-notify-gitlab-plugin/README.md#configure) を取得します。
<https://user-domain/your-name/your-project-name/edit>にアクセスすると、取得できます。

#### 4.2. access token の取得

次に、Gitlab 連携に必要なトークンを取得します。

1. <http://user-domain/-/profile/personal_access_tokens>にアクセス
2. 任意の Name, Expires at を入力
3. Scopes に `api`を選択
   - ※スコープの指定は特にありませんでしたが、`api`のみを指定してみたところ、問題なく動作しました。
4. `Create personal access token`をクリック
5. 画面上部に表示されたトークンを`regconfig.json`の`privateToken`に指定

これらの値を使い、regconfig.json を以下のように変更しました。

```regconfig.json
    "reg-notify-gitlab-plugin": {
      "projectId": "1234",
      "privateToken": "abcdef1234567890",
      "gitlabUrl": "http://user-domain"
    },
```

#### 4.3. Gitlab CI/CD の環境変数の設定

Gitlab CI/CD で AWS のコマンドを使用するために環境変数の設定行います。
[こちら](http://product-ci.works-hi/help/ci/cloud_deployment/index.md#run-aws-commands-from-gitlab-cicd)を参考に 3 つの環境変数を指定すれば OK です。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/d347d6f7-9acb-3ac5-979a-268fec89d4d4.png)

#### 4.4. .gitlab-ci.yml の作成

最後に、[reg-suit のサンプル](https://github.com/reg-viz/reg-suit#workaround-for-detached-head)と[puppeteer のサンプル](https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md#running-on-alpine)を参考に、Alpine image を使用してビルドを書いていきます。

```.gitlab-ci.yml
image: alpine:edge

cache:
  paths:
    - node_modules

# Tell Puppeteer to skip installing Chrome. We'll be using the installed package.
# Ref. https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md#running-on-alpine
variables:
  PUPPETEER_SKIP_CHROMIUM_DOWNLOAD: "true"
  PUPPETEER_EXECUTABLE_PATH: "/usr/bin/chromium-browser"

test:
  # Add AWS CLI
  # Ref. https://stackoverflow.com/questions/61918972/how-to-install-aws-cli-on-alpine#:~:text=1%2Dalpine%20.,69%20etc.
  # Add Puppeteer
  # Ref. https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md#running-on-alpine
  before_script:
    - apk update
    - |
      apk add --no-cache \
              python3 \
              py3-pip \
              git \
              chromium \
              nss \
              freetype \
              freetype-dev \
              harfbuzz \
              ca-certificates \
              ttf-freefont \
              nodejs \
              yarn \
              npm \
          && pip3 install --upgrade pip \
          && pip3 install \
              awscli \
          && rm -rf /var/cache/apk/*
    # Used by reg-keygen-git-hash-plugin
    - git checkout $CI_COMMIT_REF_NAME || git checkout -b $CI_COMMIT_REF_NAME && git pull
    - yarn

  script:
    - yarn run storycap
    - yarn run reg-suit
```

この状態で Push を行うと、無事画像がアップロードされます。

```log
$ yarn run reg-suit
yarn run v1.22.10
$ reg-suit run
[reg-suit] info version: 0.10.10
[reg-suit] info Detected the previous snapshot key: '6397a389f4e7b0c5ff39961e967bc8537fab9cde'
[reg-suit] info Comparison Complete
[reg-suit] info    Changed items: 0
[reg-suit] info    New items: 59
[reg-suit] info    Deleted items: 0
[reg-suit] info    Passed items: 0
[reg-suit] info The current snapshot key: 'd78704895f5175f7e193e6ff22c50bfdcf096d78'
[reg-publish-s3-plugin] info Upload 63 files to reg-publish-bucket-xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx.
[reg-suit] info Published snapshot 'd78704895f5175f7e193e6ff22c50bfdcf096d78' successfully.
[reg-suit] info Report URL: https://reg-publish-bucket-xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx.s3.amazonaws.com/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/index.html
[reg-notify-gitlab-plugin] warn There's no opened merge requests including the commit d78704895f5175f7e193e6ff22c50bfdcf096d78 ...
Done in 22.26s.
Saving cache for successful job
01:28
Creating cache default...
node_modules: found 225385 matching files and directories
Uploading cache.zip to https://development-infra-gitlab-ci-cache-master.s3.dualstack.ap-northeast-1.amazonaws.com/project/1111/default
Created cache
Cleaning up file based variables
00:01
Job succeeded
```

ブランチ切り替え後、適当にファイルを変更して再度 Push すると、以下のように変化しました。うまく差分比較できているようです。

```log
[reg-suit] info Comparison Complete
[reg-suit] info    Changed items: 7
[reg-suit] info    New items: 0
[reg-suit] info    Deleted items: 0
[reg-suit] info    Passed items: 52
```

Gitlab の MR のコメントはこの通り。

![comment.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/6d892731-0cb5-2f07-08c4-ba14426d491d.png)

※Gitlab CI/CD に不慣れなため、これが限界でした。
※よりよい方法があればご教示いただけますと幸いです。

## トラブルシューティング

### 1. timed out

```log
$ yarn storycap
yarn run v1.22.0
$ storycap --serverCmd "start-storybook -p 6006" http://localhost:6006
info Wait for connecting storybook server http://localhost:6006.
error Timed out waiting for: http-get://localhost:6006
Error: Timed out waiting for: http-get://localhost:6006
    at MergeMapSubscriber.project (/mnt/c/Users/works/my_repo/node_modules/wait-on/lib/wait-on.js:130:25)
    at MergeMapSubscriber._tryNext (/mnt/c/Users/works/my_repo/node_modules/wait-on/node_modules/rxjs/internal/operators/mergeMap.js:67:27)
    at MergeMapSubscriber._next (/mnt/c/Users/works/my_repo/node_modules/wait-on/node_modules/rxjs/internal/operators/mergeMap.js:57:18)
    at MergeMapSubscriber.Subscriber.next (/mnt/c/Users/works/my_repo/node_modules/wait-on/node_modules/rxjs/internal/Subscriber.js:66:18)
    at AsyncAction.dispatch (/mnt/c/Users/works/my_repo/node_modules/wait-on/node_modules/rxjs/internal/observable/timer.js:31:16)
    at AsyncAction._execute (/mnt/c/Users/works/my_repo/node_modules/wait-on/node_modules/rxjs/internal/scheduler/AsyncAction.js:71:18)
    at AsyncAction.execute (/mnt/c/Users/works/my_repo/node_modules/wait-on/node_modules/rxjs/internal/scheduler/AsyncAction.js:59:26)
    at AsyncScheduler.flush (/mnt/c/Users/works/my_repo/node_modules/wait-on/node_modules/rxjs/internal/scheduler/AsyncScheduler.js:52:32)
    at listOnTimeout (internal/timers.js:549:17)
    at processTimers (internal/timers.js:492:7)
error Command failed with exit code 1.
```

Puppeteer の起動に時間がかかっている模様です。
[serverTimeout オプション](https://github.com/reg-viz/storycap#command-line-options)を追加しました。
デフォルトが`20000`(20 秒)なので、`60000`(60 秒)に。

### 2. WSL で storycap が実行できない

```log
$ yarn storycap
yarn run v1.22.0
$ storycap --serverCmd "start-storybook -p 6006" http://localhost:6006 --serverTimeout 60000
info Wait for connecting storybook server http://localhost:6006.
error Failed to launch the browser process!
/mnt/c/Users/works/my_repo/node_modules/puppeteer/.local-chromium/linux-818858/chrome-linux/chrome: error while loading shared libraries: libxkbcommon.so.0: cannot open shared object file: No such file or directory


TROUBLESHOOTING: https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md

Error: Failed to launch the browser process!
/mnt/c/Users/works/my_repo/node_modules/puppeteer/.local-chromium/linux-818858/chrome-linux/chrome: error while loading shared libraries: libxkbcommon.so.0: cannot open shared object file: No such file or directory


TROUBLESHOOTING: https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md

    at onClose (/mnt/c/Users/works/my_repo/node_modules/puppeteer-core/lib/cjs/puppeteer/node/BrowserRunner.js:193:20)
    at Interface.<anonymous> (/mnt/c/Users/works/my_repo/node_modules/puppeteer-core/lib/cjs/puppeteer/node/BrowserRunner.js:183:68)
    at Interface.emit (events.js:327:22)
    at Interface.close (readline.js:416:8)
    at Socket.onend (readline.js:194:10)
    at Socket.emit (events.js:327:22)
    at endReadableNT (_stream_readable.js:1221:12)
    at processTicksAndRejections (internal/process/task_queues.js:84:21)
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

WSL(Windows Subsystem Linux)だと Puppeteer の名前解決ができない模様。
Powershell で実行したらいけました。

### 3. Powershell で`reg-suit run`が動かない

```ps
>  yarn reg-suit
yarn run v1.22.0
$ reg-suit run
[reg-suit] info version: 0.10.10
'grep' �́A�����R�}���h�܂��}���h�A
����\�ȃv���܂��̓o�b�` �t�@�C�������Ă��܂���B
[reg-keygen-git-hash-plugin] error Command failed: git branch | grep "^\*" | cut -b 3-
'grep' �́A�����R�}���h�܂��}���h�A
����\�ȃv���܂��̓o�b�` �t�@�C�������Ă��܂���B

[reg-suit] warn Failed to detect the previous snapshot key
[reg-suit] info Skipped to fetch the expected data because expected key is null.
[reg-suit] info Comparison Complete
[reg-suit] info    Changed items: 0
[reg-suit] info    New items: 60
[reg-suit] info    Deleted items: 0
[reg-suit] info    Passed items: 0
Error: Fail to detect the current branch.
    at CommitExplorer.getCurrentCommitHash (C:\Users\works\my_repo\node_modules\reg-keygen-git-hash-plugin\lib\commit-explorer.js:43:19)
    at GitHashKeyGenPlugin.getActualKey (C:\Users\works\my_repo\node_modules\reg-keygen-git-hash-plugin\lib\index.js:33:47)
    at RegProcessor.getActualKey (C:\Users\works\my_repo\node_modules\reg-suit-core\lib\processor.js:105:18)
    at C:\Users\works\my_repo\node_modules\reg-suit-core\lib\processor.js:27:31
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

原因は不明。WSL で実行できたため、それで良しとします。

### 4. Gitlab CI/CD で `yarn run storycap` 実行時に puppeteer のエラー

```log
$ yarn run storycap
yarn run v1.22.10
$ storycap --serverCmd "yarn storybook" http://localhost:6006 --serverTimeout 600000
info Wait for connecting storybook server http://localhost:6006.
error Failed to launch the browser process! spawn /builds/project/my_repo/node_modules/puppeteer/.local-chromium/linux-818858/chrome-linux/chrome ENOENT
TROUBLESHOOTING: https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md
Error: Failed to launch the browser process! spawn /builds/project/my_repo/node_modules/puppeteer/.local-chromium/linux-818858/chrome-linux/chrome ENOENT
TROUBLESHOOTING: https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md
    at onClose (/builds/project/my_repo/node_modules/puppeteer-core/lib/cjs/puppeteer/node/BrowserRunner.js:193:20)
    at ChildProcess.<anonymous> (/builds/project/my_repo/node_modules/puppeteer-core/lib/cjs/puppeteer/node/BrowserRunner.js:185:85)
    at ChildProcess.emit (events.js:315:20)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:275:12)
    at onErrorNT (internal/child_process.js:465:16)
    at processTicksAndRejections (internal/process/task_queues.js:80:21)
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
Cleaning up file based variables
00:00
ERROR: Job failed: exit code 1
```

[こちら](https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md#running-on-alpine)を参考に、インストール済みの Chrome を使うように設定します。

```yaml
variables:
  PUPPETEER_SKIP_CHROMIUM_DOWNLOAD: "true"
  PUPPETEER_EXECUTABLE_PATH: "/usr/bin/chromium-browser"
```

### 5. Gitlab CI/CD で `yarn run reg-suit` 実行時に Plugin のエラー

```log
$ yarn run reg-suit
yarn run v1.22.10
$ reg-suit run
[reg-suit] info version: 0.10.10
[reg-keygen-git-hash-plugin] error Can't detect branch name because HEAD is on detached commit node.
[reg-suit] warn Failed to detect the previous snapshot key
[reg-suit] info Skipped to fetch the expected data because expected key is null.
[reg-suit] info Comparison Complete
[reg-suit] info    Changed items: 0
[reg-suit] info    New items: 59
[reg-suit] info    Deleted items: 0
[reg-suit] info    Passed items: 0
Error: Fail to detect the current branch.
    at CommitExplorer.getCurrentCommitHash (/builds/project/my_repo/node_modules/reg-keygen-git-hash-plugin/lib/commit-explorer.js:43:19)
    at GitHashKeyGenPlugin.getActualKey (/builds/project/my_repo/node_modules/reg-keygen-git-hash-plugin/lib/index.js:33:47)
    at RegProcessor.getActualKey (/builds/project/my_repo/node_modules/reg-suit-core/lib/processor.js:105:18)
    at /builds/project/my_repo/node_modules/reg-suit-core/lib/processor.js:27:31
error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

ブランチ名が検出できない模様。
[こちら](https://github.com/reg-viz/reg-suit#workaround-for-detached-head)を参考に、`git checkout $CI_COMMIT_REF_NAME || git checkout -b $CI_COMMIT_REF_NAME && git pull` を追加することで解消しました。
