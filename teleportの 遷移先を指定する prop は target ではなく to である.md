新機能 `teleport` について、いろいろな記事で `<teleport target="selector">` と紹介されていますが、
`<teleport>` の prop は `target` ではなく `to` のようです(少なくとも 2020 年 7 月時点では)。
ref. [vue-next/packages/runtime-core/src/components/Teleport.ts](https://github.com/vuejs/vue-next/blob/master/packages/runtime-core/src/components/Teleport.ts#L17)

```App.vue
<teleport to="#target">
  <div>teleport</div>
</teleport>
```

また、遷移先は `<div id="app">` の外側ではないといけません。
ref. [issues/992](https://github.com/vuejs/vue-next/issues/992#issuecomment-616126089)

```index.html
<body>
  <div id="app"></div>
  <div id="target"></div>
</body>
```
