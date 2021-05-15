## はじめに

DynamoDB から CLI で項目を取得する際、通常は[`aws dynamodb query`](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html)を使用するかと思います。

しかし、`aws dynamodb query`でパーティションキーを用いた絞り込みを行う場合、上記のページにあるように完全一致(`=`)しか使用することができません。

そのため、パーティションキーを部分一致で絞り込んだ上で項目を出力したい場合は、代わりに[`aws dynamodb scan`](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html) を使用する必要があります。

## 結論

例えば、以下のように「`your_partition_key`の値が`foo`から始まる」条件で検索したい場合、

1. command を query から scan に変更
2. `--key-condition-expression`オプションを`--filter-expression`に変更

のように修正します。

```shell
# Before
$ aws dynamodb query \
    --table-name your_table_name \
    --key-condition-expression 'begins_with(your_partition_key, :your_partition_key)' \
    --expression-attribute-values '{":your_partition_key":{"S":"foo"}}'

#  An error occurred (ValidationException) when calling the Query operation: Query key condition not supported
```

```shell
# After
$ aws dynamodb scan \
    --table-name your_table_name \
    --filter-expression 'begins_with(your_partition_key, :your_partition_key)' \
    --expression-attribute-values  '{":your_partition_key":{"S":"foo"}}'

# {
#     "Items": [
#         {...}
#      ]
```

参考

- [query — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html)
- [scan — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html)

### フィルターに使用できる条件式

参考: [比較演算子および関数リファレンス - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Expressions.OperatorsAndFunctions.html#)

全部は試していませんが、以下が動作することは確認しました。

- attribute_exists
- attribute_not_exists
- begins_with
- contains

## 注意点

### DynamoDB の項目のデータ量の合計が 1MB を超える場合、1 度に フィルター結果を全件取得できない

参考: [テーブルクエリ結果をページ分割する - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Query.Pagination.html)

DynamoDB の項目のデータ量の合計が 1MB を超える場合、1 度目のレスポンスに含まれる`LastEvaluatedKey`を用いて、再度レスポンスを取得する必要があります。
(以下、一部上記のページより抜粋)

```sh
# 最初のリクエスト
$ aws dynamodb query --table-name Movies \
    --projection-expression "title" \
    --key-condition-expression "#y = :yyyy" \
    --expression-attribute-names '{"#y":"year"}' \
    --expression-attribute-values '{":yyyy":{"N":"1993"}}' \
    --page-size 5 \
    --debug

# 2017-07-07 11:13:15,603 - MainThread - botocore.parsers - DEBUG - Response body:
# b'{"Count":5,"Items":[{"title":{"S":"A Bronx Tale"}},
# {"title":{"S":"A Perfect World"}},{"title":{"S":"Addams Family Values"}},
# {"title":{"S":"Alive"}},{"title":{"S":"Benny & Joon"}}],
# "LastEvaluatedKey":{"year":{"N":"1993"},"title":{"S":"Benny & Joon"}},
# "ScannedCount":5}'

# 上で返却された LastEvaluatedKey を、queryに含めると、続きから取得可能
$ aws dynamodb query --table-name Movies \
    --projection-expression "title" \
    --key-condition-expression "#y = :yyyy" \
    --expression-attribute-names '{"#y":"year"}' \
    --expression-attribute-values '{":yyyy":{"N":"1993"}}' \
    --page-size 5 \
    --last-evaluated-key '{"year":{"N":"1993"},"title":{"S":"Benny & Joon"}}' \ # <- Here
    --debug
```

こちらの挙動は`scan`でも`query`でも共通です。
しかし、しきい値の 1MB は、`query`の`KeyConditionExpression`の適用後、`query`, `scan`の`FilterExpression`適用前に適用されます。
そのため、`scan`を使用した場合は、この制限が`query`で絞り込む場合よりもかかりやすくなります。

> 1Query オペレーションで、最大 1 MB のデータを取得できます。この制限は、結果への FilterExpression の適用前に適用されます。レスポンスに LastEvaluatedKey が存在し、Null 以外の場合、結果セットをページ分割する必要があります (テーブルクエリ結果をページ分割する を参照)。

参考: [DynamoDB でのクエリの操作 - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Query.html)

### `QueryFilter`, `KeyConditions` はレガシーパラメータなので注意

上記で紹介したように、それぞれ以下のパラメータを使用します。

- `QueryFilter` -> `FilterExpression`
- `KeyConditions` -> `KeyConditionExpression`

## 参考まとめ

- [query — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html)
- [scan — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html)

- [DynamoDB でのスキャンの使用 - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Scan.html)
- [DynamoDB でのクエリの操作 - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Query.html#FilteringResults)
- [テーブルクエリ結果をページ分割する - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Query.Pagination.html)
- [ScanFilter - Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LegacyConditionalParameters.ScanFilter.html)

- [amazon web services - Dynamodb query error - Query key condition not supported - Stack Overflow](https://stackoverflow.com/questions/31830888/dynamodb-query-error-query-key-condition-not-supported)
- [AWS Developer Forums: Query key condition not supported when querying from Lambda](https://forums.aws.amazon.com/thread.jspa?threadID=231708)
