# Vue3 で公開された実験的機能の紹介

## はじめに

9/18 に [v3.0.0 One Piece](https://github.com/vuejs/vue-next/releases/tag/v3.0.0) が公開されました。
その中に `Experimental Features` として以下の 2 つが挙げられていたため、簡単に紹介してみようと思います。
※既に Vue v3.0.0 で利用できます。
※記事作成時の仕様となります。今後変わる場合もあります。

(原文)

> We have proposed two new features for Singe-File Components (SFC, aka .vue files):
>
> - \<script setup>: syntactic sugar for using Composition API inside SFCs
> - \<style vars>: state-driven CSS variables inside SFCs
>
> These features are already implemented and available in Vue 3.0, but are provided only for the purpose of gathering feedback. They will remain experimental until the RFCs are merged.

## 1. \<script setup>: syntactic sugar for using Composition API inside SFCs

### 概要

詳細は[こちら](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md)を参照してください。
[Composition API の setup 関数](https://composition-api.vuejs.org/api.html#setup)のシンタックスシュガーになります。
具体的には、

```vue
<script>
import { ref } from "vue";

export default {
  setup() {
    const count = ref(0);
    const inc = () => count.value++;

    return {
      count,
      inc,
    };
  },
};
</script>
```

これが、

```vue
<script setup>
import { ref } from "vue";

export const count = ref(0);
export const inc = () => count.value++;
</script>
```

これになります。
setup 関数の定型文が大幅に省略できることが分かります。
また、逐次`export`できるため、最後にまとめて`return`する必要がなくなります。

### Component の export

こちらもシンプルに書けます。

```vue
<template>
  <Foo />
  <Bar />
  <component :is="ok ? Foo : Bar" />
</template>

<script setup>
export { default as Foo } from "./Foo.vue";
export { default as Bar } from "./Bar.vue";
export const ok = Math.random();
</script>
```

### props の宣言と他のオプションの使い方

setup 関数以外の option を利用するためには、`export default` を使用します。
また、setup 関数の中で props を使用する場合は、 `<script setup="props">` のように記述します。

```vue
<script setup="props">
import { computed } from "vue";

export default {
  props: {
    msg: String,
  },
  inheritAttrs: false,
};

export const computedMsg = computed(() => props.msg + "!!!");
</script>
```

※Compile の都合上、`export default`内でこの外で宣言された値(ex. `computedMsg`)を使用できません。

### Typescript のサポート

`<script setup>`のほとんどが Typescript で動作します。
`props` や `emit`等の setup 関数の引数を使用する場合は`declare`で宣言します。

```vue
<script setup="props, context" lang="ts">
import { computed, SetupContext } from "vue";

// declare props using TypeScript syntax
// this will be auto compiled into runtime equivalent!
declare const props: {
  msg: string;
};
declare const context: SetupContext;

export const computedMsg = computed(() => props.msg + "!!!");
export const onClick = () => context.emit("click", "sample");
</script>
```

※しかし、この方法で `props` の型を定義する場合、`props` の `default` や `required` を指定できませんでした。
`export default`で定義しても、`declare` の設定で上書きしてしまうようです。
もし指定方法を知っている方がいらっしゃいましたら、コメントいただけると嬉しいです。

#### Typescript 使用時、Component を import(re-exporting) する際に気をつけること

通常、`<script setup>`を使用する際は`export default`は記述する必要はありません。
しかし、その Component を import する側が Typescript を使用している場合、`export default`を使っていないと Vetur がエラーを吐きます。
そのため、`export default {}`と空の Object を return しましょう。

```vue
<!--子-->
<script setup></script>

<!--親-->
<script setup>
// Module '"../components/Child.vue"' has no exported member 'default'.Vetur(2305)
export { default as Child } from "@/components/Child.vue";
</script>
```

### 通常の\<script>との併用

`<script setup>`を使用した場合、内部の Script は全て setup 関数でラップされてしまいます。
そのため、他のファイルから import できる値の宣言や、一度しか実行したくない関数の実行をしたい場合は、`<script>`と`<script setup>`を併用します。

```vue
<script>
performGlobalSideEffect();

// this can be imported as `import { named } from './*.vue'`
export const named = 1;
</script>

<script setup>
import { ref } from "vue";

export const count = ref(0);
</script>
```

## 2. \<style vars>: state-driven CSS variables inside SFCs

後ほど追記します。
詳細は[こちら](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md)を参照してください。
