# 【JMESPath】`foo[*][0]` と`foo[*] | [0]` の違い

## はじめに

AWS CLI の query で、パイプ(|)の挙動が分かりにくかったので整理した。

## サンプルデータ

```json
{
  "foo": [
    ["one", "two"],
    ["three", "four"],
    ["five", "six"]
  ]
}
```

## 結論

プロジェクション`foo[*]`[^1]に対し [Index Expression](https://jmespath.org/specification.html#index-expressions)(配列)でアクセスする場合、以下のように挙動が変わる。

- `foo[*][0]`
  - `["one", "three", "five"]`
- `foo[*] | [0]`
  - `["one", "two"]`

## 詳細

※<https://jmespath.org/>で実際にパスを入力しながら進めると、理解が早い。

[^1]: (JSON) document に対し、何らかの式(Expression)を反映させた結果のことを、[公式](https://jmespath.org/specification.html#wildcard-expressions)に則りプロジェクション(projection)と呼ぶ。

### 前提: foo と foo[\*]

`foo`と`foo[*]`の結果は、両方とも以下のようになる。

```json
[
  ["one", "two"],
  ["three", "four"],
  ["five", "six"]
]
```

### foo[\*][0]

`foo[*][0]`は、`foo[*]`で投影(projected)された各要素に対して`[0]`でアクセスを行う。
`foo[*]`は、`配列fooの全ての子要素を投影`するため、子要素である`["one", "two"]`, `["three", "four"]`, `["five", "six"]`が投影されている形になる。

つまり、上述したように`foo[*]`の投影結果は`foo`と同様に以下のようになる。
しかし、`foo`がこの 2 重配列全体を指しているのに対し、`foo[*]`が投影しているのはそれぞれの子配列要素である。
(複数の配列要素を投影しているで、2 重配列として表現されているに過ぎない)

```json
[
  ["one", "two"],
  ["three", "four"],
  ["five", "six"]
]
```

`foo[*][0]`は、これら 3 つの配列要素の 0 番目の要素を 1 つの配列にマージする。
そのため、結果として`["one", "three", "five"]`が投影される。

参考: [Flatten Operator](https://jmespath.org/specification.html#flatten-operator)

### foo[\*] | [0]

`|`(パイプ)を使用すると、投影された各要素を無視して、結果自体に右側の式を適用する。

`foo[*] | [0]`は、以下の`foo[*]`の投影結果全体における 0 番目の要素を返す。

```json
[
  ["one", "two"],
  ["three", "four"],
  ["five", "six"]
]
```

つまり、上の 2 重配列における 0 番目の要素である`["one", "two"]`が返る

参考: [Pipe Expressions](https://jmespath.org/specification.html#pipe-expressions)

## 蛇足と言う名の応用

上記を踏まえた上で、 `foo[*][0][0]` と`foo[*][0] | [0]`の結果がどう変わるか考えてみる。

上述したとおり、`foo[*][0]`は以下の通り。

```json
["one", "three", "five"]
```

<details><summary>答えはこちら</summary><div>

### `foo[*][0][0]`

#### 結果

[]

#### 解説

`foo[*][0]`が投影しているのは、`"one", "three", "five"`という 3 つの文字列要素である。
文字列要素に`[0]`でアクセスしても`null`になる。
投影された値を 1 つの配列にマージする際、`null`は無視されるため、結果として空配列`[]`が返る。

### `foo[*][0] | [0]`

#### 結果

"one"

#### 解説

`["one", "three", "five"]`という配列全体に対して、`[0]`でアクセスしたため、0 番目の要素である`"one"`が返る。

</div></details>

参考: [Slices](https://jmespath.org/specification.html#slices)

## 参考

- [Index Expressions | JMESPath Specification](https://jmespath.org/specification.html#index-expressions)
- [JMESPath で配列から要素を抽出するにはパイプを用いる](https://qiita.com/cold-wisteria/items/15f0369544b9de5d3952)
