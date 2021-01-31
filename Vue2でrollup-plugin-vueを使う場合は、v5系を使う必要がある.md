# rollup-plugin-vue でビルドしたコンポーネントを Vue2 アプリで使用する場合は、rollup-plugin-vue v5 系を使う必要がある

## 結論

```diff
-   yarn add -D rollup-plugin-vue
+   yarn add -D rollup-plugin-vue@v5.0.0
```

## 背景

2021 年 1 月現在、[rollup-plugin-vue の最新は v6.0.0](https://www.npmjs.com/package/rollup-plugin-vue)だが、これは`rollup-plugin-vue@next`と銘打ってある通り、Vue 3 にしか対応していない。

そのため、`rollup-plugin-vue` でビルドしたコンポーネントを Vue2 アプリで使用する場合は、`rollup-plugin-vue`の v5 系を使用し、ビルドして上げる必要がある。

## エラー内容

Vue で`rollup-plugin-vue`の v6 を使うと、ビルド時は特にエラーが出ない。
しかし、バンドルされたファイルを Vue2 のアプリにインストールすると、以下のようなエラーが発生する。

```log
my-component.esm.js?4799:8 Uncaught TypeError: Object(...) is not a function
    at Module.eval (my-component.esm.js?4799:8)
    at eval (my-component.esm.js:52)
    at Module../node_modules/v2-app/dist/my-component.esm.js (chunk-vendors.js:1223)
    at __webpack_require__ (app.js:849)
    at fn (app.js:151)
    at eval (MyComponent.vue?a75f:9)
    at Module../node_modules/cache-loader/dist/cjs.js?!./node_modules/babel-loader/lib/index.js!./node_modules/ts-loader/index.js?!./node_modules/cache-loader/dist/cjs.js?!./node_modules/vue-loader/lib/index.js?!./src/components/MyComponent.vue?vue&type=script&lang=ts& (app.js:950)
    at __webpack_require__ (app.js:849)
    at fn (app.js:151)
    at eval (MyComponent.vue?1254:1)
```

`resolveComponent`, `createVNode`, `createBlock` のような Vue3 にしか存在しないメソッドをバンドルファイル内で使用している模様。

参考: <https://github.com/vuejs/rollup-plugin-vue/issues/363>

## 最後に

~~リポジトリ分けるか、オプションで切り替えられるようにした欲しかった。~~
