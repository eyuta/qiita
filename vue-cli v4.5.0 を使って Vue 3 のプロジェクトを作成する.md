
## はじめに

昨日(7/24)に、[vue-cli v4.5.0](https://github.com/vuejs/vue-cli/releases/tag/v4.5.0) がリリースされました。
これにより、vue-cli から一発で Vue 3 のプロジェクトが作れるようになりました。
(今までは、Vue 2 のプロジェクトを作成した後に、Vue 3 に upgrade する必要がありました)

さっそく、プロジェクトの作成から起動まで行ってみましょう。

## 使い方

##### 1. 最新の `@vue/cli` をグローバルにインストールする

```sh
yarn global add @vue/cli@next
# OR
npm install -g @vue/cli@next
```

既にプロジェクトが存在する場合は対象のプロジェクトを upgrade します。

```sh
vue upgrade --next
```

##### 2. create 時に `Vue 3` を選択する

```sh
vue create <project-name>
```

```sh
# Vue 3 のpresetを使用する場合
? Please pick a preset: (Use arrow keys)
  Default ([Vue 2] babel, eslint)
❯ Default (Vue 3 Preview) ([Vue 3] babel, eslint)
  Manually select features

# マニュアルで選択する場合
? Please pick a preset: Manually select features
? Check the features needed for your project: Choose Vue version, Babel, TS, PWA, Router, Vuex, CSS Pre-processors, Linter, Unit, E2E
? Choose a version of Vue.js that you want to start the project with
  2.x
❯ 3.x (Preview)
```

もちろん、 `vue ui` から選択することもできます。

- ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/9b442541-48d7-b55f-7ad6-7b27513315d2.png)

インストール後、 `package.json`を見ると vue 3.0 や vuex4.0 がインストールされているのが分かります。
(なんと、 `-beta` が外れています!!)

```json
  "dependencies": {
    "core-js": "^3.6.5",
    "register-service-worker": "^1.7.1",
    "vue": "^3.0.0-0",
    "vue-router": "^4.0.0-0",
    "vuex": "^4.0.0-0"
  },
```

##### 3. 起動する

おなじみの画面が起動します。

```sh
cd <project-name>
yarn serve
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/431b3900-da50-2e6b-30ae-fc1a0f0e2f5a.png)

## 最後に

[起動時にエラー](https://github.com/vuejs/vue-cli/issues/5712)が出るなど、不安定ではありますが CLI から Vue 3 のプロジェクトをかんたんに作れるようになりました。
8 月上旬の Vue 3 正式リリースへの期待も高まりますね。
