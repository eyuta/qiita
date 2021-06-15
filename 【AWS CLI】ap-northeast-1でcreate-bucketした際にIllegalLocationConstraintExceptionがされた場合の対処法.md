## はじめに

以下のエラーが発生した際の対処法です。

```sh
$ aws s3api create-bucket --bucket test-bucket

# An error occurred (IllegalLocationConstraintException) when calling the CreateBucket operation: The unspecified location constraint is incompatible for the region specific endpoint this request was sent to.

```

## 結論

`--create-bucket-configuration`に`LocationConstraint=$region`を指定する必要があります。
(今回であれば`--create-bucket-configuration LocationConstraint=ap-northeast-1`)

```sh
$ aws s3api create-bucket --bucket test-bucket --create-bucket-configuration LocationConstraint=ap-northeast-1

# {
#     "Location": "http://test-bucket.s3.amazonaws.com/"
# }
```

## 詳細

[公式リファレンス](https://docs.aws.amazon.com/cli/latest/reference/s3api/create-bucket.html)に全て記載があります。

> LocationConstraint:
>
> > Specifies the Region where the bucket will be created. If you don't specify a Region, the bucket is created in the US East (N. Virginia) Region (us-east-1).

`LocationConstraint`は、バケットが作成されるリージョンを指定します。
`LocationConstraint`を指定しない場合、バケットは米国東部 (バージニア北部) リージョン (`us-east-1`) に作成されます。

> > If you send your create bucket request to the s3.amazonaws.com endpoint, the request goes to the us-east-1 Region.
> > ~~
> > Regions outside of us-east-1 require the appropriate LocationConstraint to be specified in order to create the bucket in the desired region:

`create-bucket`リクエストは、デフォルトでは `us-east-1`(バージニア北部) リージョンに送信されます。

そのため、`us-east-1` 以外にバケットを作成する場合は、そのリージョンを `LocationConstraint` に指定する必要があります。

逆に言えば、`us-east-1`からリクエストを送信する場合は、`--create-bucket-configuration`を指定する必要がありません。
またその場合は、`us-east-1`にバケットが作成されます。

```sh
$ aws s3api create-bucket --bucket test-bucket2 --region us-east-1

# {
#     "Location": "/test-bucket2"
# }


```

## 参考

- [create-bucket — AWS CLI 1.19.84 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/s3api/create-bucket.html)
- [aws s3api create-bucket throws error for us-east-2 · Issue #2603 · aws/aws-cli · GitHub](https://github.com/aws/aws-cli/issues/2603#issuecomment-422271388)
