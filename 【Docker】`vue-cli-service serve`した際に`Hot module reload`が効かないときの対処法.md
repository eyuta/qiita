# 【Docker】`vue-cli-service serve`した際に`Hot module reload`が効かないときの対処法

## 結論

以下を追加します。

```vue.config.js
module.exports = {
  devServer: {
    watchOptions: {
      poll: true
    }
  }
};

```

## 環境

Docker Desktop: Version 3.1.0
Alpine: Version 3.12
@vue/cli: 4.5.11

```package.json
  "dependencies": {
    "core-js": "^3.6.5",
    "vue": "^2.6.11"
  },
  "devDependencies": {
    "@typescript-eslint/eslint-plugin": "^2.33.0",
    "@typescript-eslint/parser": "^2.33.0",
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-plugin-typescript": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "@vue/eslint-config-prettier": "^6.0.0",
    "@vue/eslint-config-typescript": "^5.0.2",
    "eslint": "^6.7.2",
    "eslint-plugin-prettier": "^3.1.3",
    "eslint-plugin-vue": "^6.2.2",
    "prettier": "^1.19.1",
    "typescript": "~3.9.3",
    "vue-template-compiler": "^2.6.11"
  }

```

## 問題

> Control options related to watching the files.
>
> webpack uses the file system to get notified of file changes. In some cases, this does not work. For example, when using Network File System (NFS). Vagrant also has a lot of problems with this. In these cases, use polling:

引用: [devServer.watchOptions](https://webpack.js.org/configuration/dev-server/#devserverwatchoptions-)

Vagrant 等のネットワークファイルシステム（NFS）使っている場合、webpack がファイル変更の通知を受け取れないことがあるらしいです。
Docker でも発生する模様。
そのため、定期的にポーリングを行うことでこの問題を解消します。

この[Issue](https://github.com/vuejs/vue-cli/issues/2051#issuecomment-420320469)が参考になりました。
