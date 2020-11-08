# VueのComposition APIをTypescriptで使う際に覚えておくと便利なこと

## はじめに

Composition API を Typescript で使う際にハマったことの備忘録です。
随時更新していく予定。

Vue3 の環境構築は[こちら](https://qiita.com/eyuta/items/ba9a5dc5cc35e733853e)から。vue-cli からかんたんに Vue3 環境を作成できます。

## Version

```sh
Vue: 3.0.0
@vue/cli: 4.5.0
Typescript: 3.9.3
```

## Tips

### 1. Recursive Type References

[Composition API RFC](https://composition-api.vuejs.org/#basic-example) のコードをそのまま Typescript で実行すると、以下のエラーが表示されます。
`state` の定義中に、 自分自身(`state`)を使用しているためです。
(Typescript のバージョンによっては発生しないらしいです)

```txt
'state' implicitly has type 'any' because it does not have a type
annotation and is referenced directly or indirectly in its own initializer.
```

```ts
setup() {
  const state = reactive({
    count: 0,
    double: computed(() => state.count * 2)
  });
  return { state };
}
```

```ts
// use interface
interface Counter {
  count: number;
  double: number;
}
setup() {
  const state: Counter = reactive({
    count: 0,
    double: computed(() => state.count * 2)
  });
  return { state };
}

```

```ts
// use ref
setup() {
  const count = ref(0);
  const double = computed(() => count.value * 2);

  function increment() {
    count.value++;
  }

  return {
    count,
    double,
    increment
  };
}
```

ref. [Typescript state auto-typing and implicitly has type 'any' because it does not have a type annotation and is referenced directly or indirectly in its own initializer #131](https://github.com/vuejs/composition-api/issues/131)

### 2. `props` で型推論が効くようにする

そのままだと `props` にほとんど補完が効かないため、 `props` に `ComponentObjectPropsOptions` を付与します。
こうすることで、 `props` に指定した `prop` に対し、 `default`, `required` といった property が補完されます。

```ts
import { defineComponent, ComponentObjectPropsOptions } from "vue";

interface CustomProps {
  msg: string;
}
export default defineComponent({
  name: "Home",
  props: {
    msg: {
      required: true,
      type: String,
      default: "",
    },
  } as ComponentObjectPropsOptions<CustomProps>,
});
```

以下の図で、 `required` が推論されているのが分かります。

- before
  ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/27a12b24-77a2-1334-18f8-225fb1307585.png)
- after
  ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/384adff4-f0a0-680d-a71e-a5e917a0dd62.png)

また、`defineComponent` の generics に任意の Type を与えることで、 `prop` の型を縛ることができます。
上記のコードでは、 `CustomProps.msg` の型が `string` のため、 `props.msg.type` に `String` 以外を指定するとエラーになります。
※ `defineComponent<CustomProps>` でもできそうですが、うまくいきませんでした...
うまい方法を知っている方がいらっしゃったらご教示いただけると嬉しいです。
