## はじめに

以下のような複数の json オブジェクトを、`jq`を用いて 1 つの配列に格納する方法を調べました。

```json
{ "name": "JSON" }
{ "name": "XML" }
```

## 結論

`-s` オプション、あるいは `-n` オプションを用います。

### `-s` オプション

```shell
$ echo '
    { "name": "JSON" }
    { "name": "XML" }
    ' | jq -s .
```

```output.json
[
  {
    "name": "JSON"
  },
  {
    "name": "XML"
  }
]
```

### `-n` オプション

```shell
$ echo '
    { "name": "JSON" }
    { "name": "XML" }
    ' | jq -n [inputs]
```

```output.json
[
  {
    "name": "JSON"
  },
  {
    "name": "XML"
  }
]
```

### そのまま jq に流した場合

`,` で区切られずに出力されます。

```shell
$ echo '
    { "name": "JSON" }
    { "name": "XML" }
    ' | jq .
```

```output.json
{
  "name": "JSON"
}
{
  "name": "XML"
}
```

### `jq .[]` で出力される結果を、配列に格納する場合

`jq .[]` で出力される結果を配列に格納する場合は、フィルター全体を配列で囲うことで、配列化できます。

####

```shell
$ JSON='
[
    {
        "name": "JSON",
        "good": true
    },
    {
        "name": "XML",
        "good": false
    }
]
'
```

オプション無しだと、`,`が存在しないが、

```shell
$ echo $JSON | jq '.[] | {name: .name}'

```

```output.json
{
  "name": "JSON"
}
{
  "name": "XML"
}
```

フィルター全体を`[]`で囲うことで、配列化できます。

```shell
$ echo $JSON | jq '[.[] | {name: .name}]'

```

```output.json
[
  {
    "name": "JSON"
  },
  {
    "name": "XML"
  }
]
```

もちろん、 `-s`や`-n`も使用可能。その場合は、`jq`の結果をパイプで`jq`にわたす必要があります。

```shell
$ echo $JSON \
  | jq '.[] | {name: .name}' \
  | jq -s '.'

```

```output.json
[
  {
    "name": "JSON"
  },
  {
    "name": "XML"
  }
]
```

## 詳細

### `-s` オプション

> --slurp/-s:
> Instead of running the filter for each JSON object in the input, read the entire input stream into a large array and run the filter just once.

[公式のマニュアル](https://stedolan.github.io/jq/manual/)にある通り、通常`jq`は入力ストリームを空白文字列で区切り、それぞれの json オブジェクトに対してフィルター処理を適用します。

`-s`を使用した場合、それぞれの json オブジェクトに対してフィルター処理を適用せず、入力ストリームを 1 つの大きな配列として読み込んだ後、フィルター処理を 1 度だけ実行します。

### `-n` オプション

> --null-input/-n:
> Don't read any input at all! Instead, the filter is run once using null as the input. This is useful when using jq as a simple calculator or to construct JSON data from scratch.

`-n`を使用した場合、**渡された入力ストリームを読まずに`null`を入力ストリームとして**フィルター処理を行います。

```shell
$ echo $JSON | jq '.[] | {name: .name}' | jq -n '.'
# null
```

これと、 `jq`の`I / O処理`の`inputs`を組み合わせます。

> inputs
> Outputs all remaining inputs, one by one.
>
> This is primarily useful for reductions over a program's inputs.

`inputs`に、(残り[^1]の)全ての入力ストリームが格納されているため、`inputs`を配列で囲ってあげることで、`-s`と同様の出力が可能になります。

[^1]:
    `inputs`に格納されている「残りの全ての入力ストリーム」とは
    `inputs`の他に`input`があります。`input`と`inputs`を併用している場合、`input`で出力した残りが`inputs`に格納されます。

    <details><summary>例
    </summary><div>

    ```sh
    # 全件
    $ echo $JSON | jq '.[] | {name: .name}' | jq -n '{inputs:[inputs]}'
    # {
    #   "inputs": [
    #     {
    #       "name": "JSON"
    #     },
    #     {
    #       "name": "XML"
    #     }
    #   ]
    # }
    ```

    ```sh
    # `input` を1回使用後、 `inputs` を使用する
    $ echo $JSON | jq '.[] | {name: .name}' | jq -n '{input: input, inputs:[inputs]}'
    # {
    #   "input": {
    #     "name": "JSON"
    #   },
    #   "inputs": [
    #     {
    #       "name": "XML"
    #     }
    #   ]
    # }
    ```

    ```sh
    # `input` を2回使用後、 `inputs` を使用する
    $ echo $JSON | jq '.[] | {name: .name}' | jq -n '{input: input, input2: input, inputs:[inputs]}'

    # {
    #   "input": {
    #     "name": "JSON"
    #   },
    #   "input2": {
    #     "name": "XML"
    #   },
    #   "inputs": []
    # }
    ```

    </div></details>

## 参考

- [jq Manual (development version)](https://stedolan.github.io/jq/manual/)
- [How to convert a JSON object stream into an array with jq - Stack Overflow](https://stackoverflow.com/questions/29404575/how-to-convert-a-json-object-stream-into-an-array-with-jq)
- [jq コマンドを使う日常のご紹介 - Qiita](https://qiita.com/takeshinoda@github/items/2dec7a72930ec1f658af)
