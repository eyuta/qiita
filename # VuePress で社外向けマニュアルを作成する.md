# VuePress で社外向けマニュアルを作成する

## 環境構築

ref [quick-start](https://vuepress.vuejs.org/guide/getting-started.html#quick-start)

## Troubleshooting

### config.js の変更が反映されない

`--temp .temp` を package.json の `scripts.dev` に追加する.

```diff
- "dev": "vuepress dev docs"
+ "dev": "vuepress dev docs --temp .temp"
```

refs [Hot reload not working #2392](https://github.com/vuejs/vuepress/issues/2392#issuecomment-651903508)
