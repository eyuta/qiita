## はじめに

`DynamoDBから特定のデータをエクスポートし、値を一部変更した上で再度インポートする`という要件があったので、これを AWS CLI で行ってみました。

DynamoDB のエクスポート/インポートは、[AWS Data Pipeline](https://docs.aws.amazon.com/ja_jp/datapipeline/latest/DeveloperGuide/dp-importexport-ddb.html)や[Lambda と S3](https://aws.amazon.com/jp/blogs/news/implementing-bulk-csv-ingestion-to-amazon-dynamodb/)を使用することで可能になります。
しかし、どちらもちょっと手間がかかります。
そこで、手軽にできる AWS CLI を使用して検証してみました。

## 結論

データエクスポートに[query](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html) or [scan](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html)、
データ整形に `jq`、
データインポートに [batch-write-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/batch-write-item.html)を使用しました。

スクリプトは以下の通りです。

```sh
# 初期化
next_token=

function scan() {
  # わかりやすいように scan API を関数に格納。インラインでもOK
  # batch-write-itemが一度に書き込めるのが25件なので、`--max-items 25` を使用
  # 前回のデータを取得時に、next_tokenが存在した場合はそれを指定。存在しない場合は空欄
  aws dynamodb scan \
    --table-name $your_table_name \
    --max-items 25 \
    $([ -z $next_token ] && echo "" || echo "--starting-token $next_token")
}

while [ "$next_token" != "null" ]; do
  # scan結果を、batch-write-itemで読める形に整形
  request_items=$(
    echo $cli_output \
      | jq '.Items[] | {PutRequest: {Item:.}}' \
      | jq -n "{\"$your_table_name\": [inputs]}"
  )
  aws dynamodb batch-write-item --request-items "$request_items"

  # next_token を格納 (無い場合はnullが入る)
  next_token=$(echo $cli_output | jq -r ".NextToken")
done
```

## 詳細

[scan](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html)コマンドを使用した場合、以下のような形式のデータが出力されます。

```json
{
    "Items": [
        { /** ここにデータ*/ },
        { ... },
        ...,
    ],
    "Count": 0,
    "ScannedCount": 0,
    "ConsumedCapacity": null,
    "NextToken": "aaaaaaaa" // or "null"
}
```

一方、[batch-write-item](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/batch-write-item.html)でインポートする際の形式は、以下の通りとなります。
そのため、`jq`コマンドを使用して上の形式を下の形式に変更しています。

```json
{
  "your_table_name": [
    {
      "PutRequest": {
        "Item": { /** ここにデータ*/ },
      }
    },
    {
      "PutRequest": {
        "Item": { /** ここに2件目のデータ*/ },
      }
    },
    ...,
  ]
}

```

また、scan した件数より出力した件数が少ない場合、`nextToken`が返されるので、2 回目以降はその`nextToken`を`--starting-token`パラメータに指定します。
参考: [AWS CLI ページ分割オプションの使用 - AWS コマンドラインインターフェイス](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-usage-pagination.html)

## デモ

実際にテーブルを用意し、データのエクスポートとインポートを行います。
要件は、`yaerが2013年のデータをエクスポートし、2021年に書き換えた上で、同じテーブルに違うデータとしてインポートする`とします。

### 1. 準備

[こちらのページ](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/GettingStarted.NodeJs.01.html)を参考にテーブルの作成とデータの追加を行います。

```sh
# テーブル作成
$ aws dynamodb create-table \
  --attribute-definitions '
      [
        { "AttributeName": "year", "AttributeType": "N" },
        { "AttributeName": "title", "AttributeType": "S" }
      ]' \
  --table-name Movies \
  --key-schema '
      [
        { "AttributeName": "year", "KeyType": "HASH" },
        { "AttributeName": "title", "KeyType": "RANGE" }
      ]' \
  --provisioned-throughput '
      {
          "ReadCapacityUnits": 10,
          "WriteCapacityUnits": 10
      }'
```

サンプルデータは、[こちらの`moviedata.zip`](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/GettingStarted.NodeJs.02.html#GettingStarted.NodeJs.02.01)を使います。

```sh
# データの登録
# 1 行ずつ処理するために、json オブジェクトを 1 行で表示する `-c`オプションを使用
$ IFS=$'\n'; for item in $(cat moviedata.json| jq -c '.[:20] | .[]'); do
  aws dynamodb put-item \
  --table-name Movies \
  --item  "{
    \"year\": {\"N\": \"$(echo $item | jq .year)\"},
    \"title\": {\"S\": $(echo $item | jq .title)}
  }"
done
```

### 2. データのエクスポート/インポート

```sh
# データ確認用
OUTPUT_FILE="output.json"
# 初期化
next_token=

function scan() {
  # わかりやすいように scan API を関数に格納。インラインでもOK
  # yearが2013年のデータを抽出
  # year は予約後なので、 `--expression-attribute-names` を用いて `#year`というプレースホルダーを用意する
  # 前回のデータを取得時に、next_tokenが存在した場合はそれを指定。存在しない場合は空欄
  aws dynamodb scan \
    --table-name Movies \
    --filter-expression '#year = :year' \
    --expression-attribute-values '{":year":{"N":"2013"}}' \
    --expression-attribute-names '{"#year": "year"}' \
    --max-items 25 \
    $([ -z $next_token ] && echo "" || echo "--starting-token $next_token")
}

while [ "$next_token" != "null" ]; do
  # scan結果を、year書き換え後にbatch-write-itemで読める形に整形
  request_items=$(
    echo $cli_output \
      | jq '.Items[].year.N="2021"' \
      | jq '.Items[] | {PutRequest: {Item:.}}' \
      | jq -n '{"Movies": [inputs]}'
  )
  echo $request_items >> $OUTPUT_FILE
  aws dynamodb batch-write-item --request-items "$request_items"

  # next_token を格納 (無い場合はnullが入る)
  next_token=$(echo $cli_output | jq -r ".NextToken")
done

```

実際に DB を確認すると、データが増えているのが確認できるかと思います。

## 参考

- [AWS Data Pipeline を使用した DynamoDB データのインポートとエクスポート - AWS Data Pipeline](https://docs.aws.amazon.com/ja_jp/datapipeline/latest/DeveloperGuide/dp-importexport-ddb.html)
- [Amazon DynamoDB への CSV 一括取り込みの実装 | Amazon Web Services ブログ](https://aws.amazon.com/jp/blogs/news/implementing-bulk-csv-ingestion-to-amazon-dynamodb/)
- [ステップ 1: JavaScript 用の AWS SDK を使用して DynamoDB でテーブルを作成する - Amazon DynamoDB](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/GettingStarted.NodeJs.01.html)
- [create-table — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/create-table.html)
- [put-item — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/put-item.html)
- [【jq】bash で json 配列をループさせる - Qiita](https://qiita.com/eyuta/items/21c19fd3edb89a91fe12)
- [javascript - Scan Function in DynamoDB with reserved keyword as FilterExpression NodeJS - Stack Overflow](https://stackoverflow.com/questions/36698945/scan-function-in-dynamodb-with-reserved-keyword-as-filterexpression-nodejs)
- [query — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/query.html)
- [scan — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/scan.html)
- [batch-write-item — AWS CLI 1.19.73 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/dynamodb/batch-write-item.html)
- [AWS CLI ページ分割オプションの使用 - AWS コマンドラインインターフェイス](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-usage-pagination.html)
