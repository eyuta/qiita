## はじめに

## Install

```sh
$ npm i -g @vue/cli
$ vue add typescript

✔  Successfully installed plugin: @vue/cli-plugin-typescript

? Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? Yes
? Allow .js files to be compiled? No
? Skip type checking of all declaration files (recommended for apps)? Yes
```

## トラブルシューティング

### Vuetify

#### Could not find a declaration file for module 'vuetify/lib/framework'

```log
Could not find a declaration file for module 'vuetify/lib/framework'. 'c:/Users/works/workspace/st-kanbanboard/node_modules/vuetify/lib/framework.js' implicitly has an 'any' type.
  Try `npm i --save-dev @types/vuetify` if it exists or add a new declaration (.d.ts) file containing `declare module 'vuetify/lib/framework';`ts(7016)

```

[こちら](https://stackoverflow.com/questions/65172139/could-not-find-a-declaration-file-for-module-vuetify-lib-framework/65172140)を参考に、以下のように変更した。

```ts
import Vuetify from "vuetify/lib/framework";
```

to

```ts
import Vuetify from "vuetify/lib";
```

#### Could not find a declaration file for module 'vuetify/lib'

```log
Could not find a declaration file for module 'vuetify/lib'. 'c:/Users/works/workspace/st-kanbanboard/node_modules/vuetify/lib/index.js' implicitly has an 'any' type.
  Try `npm i --save-dev @types/vuetify` if it exists or add a new declaration (.d.ts) file containing `declare module 'vuetify/lib';`ts(7016)

```

[こちら](https://github.com/vuetifyjs/vue-cli-plugins/issues/112#issuecomment-562935079)を参考に、`tsconfig.json`に以下を追加した。

```json
  "types": [
      ....
      "vuetify"
  ]
```
