## はじめに

rollup の Plugin は移ろいが早いので注意が必要、という話です。

## 1. 2019 年 ~ 2020 年初めに、rollup の公式 Plugin のほとんどが deprecated になった

既存の Plugin が軒並み deprecated になり、rollup 本家のリポジトリに移動されました。
例:

- [rollup-plugin-commonjs](https://github.com/rollup/rollup-plugin-commonjs)は deprecated
  - [@rollup/plugin-commonjs](https://github.com/rollup/plugins/tree/master/packages/commonjs)を使う
- [rollup-plugin-buble](https://
- github.com/rollup/rollup-plugin-buble)は deprecated
  - [@rollup/plugin-buble](https://github.com/rollup/plugins/tree/master/packages/buble)を使う

これ以外の `rollup/rollup-plugin-XXXX`も、全て `rollup/plugins`配下に移動しています。
過去記事だと`rollup/rollup-plugin-XXXX`を参照しているものも多いので注意が必要です。

## 2. TypeScript 用の Plugin が乱立しているため

- [@rollup/plugin-typescript](https://github.com/rollup/plugins/tree/master/packages/typescript/#rollupplugin-typescript)
  - 本家
- [ezolenko/rollup-plugin-typescript2](https://github.com/ezolenko/rollup-plugin-typescript2)
  - 3rd Party 製の Plugin だが、DL 数は本家に比べてこっちのほうが 4 倍くらい多い
- [rollup/rollup-plugin-typescript](https://github.com/rollup/rollup-plugin-typescript)
  - deprecated
- これ以外にもいっぱい

現状、本家の[@rollup/plugin-typescript](https://github.com/rollup/plugins/tree/master/packages/typescript/#rollupplugin-typescript)を使うのが良さそうです。

これらが乱立する経緯は、[こちら](https://github.com/rollup/plugins/issues/541)が分かりやすいです。
私は以下のように理解しました。

1. 公式で`rollup/rollup-plugin-typescript`が用意されたものの、TypeScript 1.X にか対応していなかった
2. TypeScript 2.X でも利用するため、3rd Party の Plugin が乱立
3. [ezolenko/rollup-plugin-typescript2](https://github.com/ezolenko/rollup-plugin-typescript2)がスタンダードになる
4. 公式が最新の TypeScript に対応した[@rollup/plugin-typescript](https://github.com/rollup/plugins/tree/master/packages/typescript/#rollupplugin-typescript)をリリース

## 3. rollup-plugin-vue でビルドした Component を Vue2 アプリで使用する場合は、rollup-plugin-vue v5 系を使う必要がある

[こちら](https://qiita.com/eyuta/items/5f68e65e3fafe9eb5c78)にまとめました。

逆に言えば、Vue3 アプリ用にビルドしたい場合は、最新(`rollup-plugin-vue v6`)を使います。
