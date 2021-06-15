## はじめに

Bashで変数を使用するには`"`を使う必要があるため、シンプルに書くと以下のようになります。
が、これだと見にくく、追加が面倒くさいです。
そこで良い方法が無いか調べてみました。

```sh
$ REPOSITORY="foo"
$ BRANCH="bar"
$ SCOPE="baz"

$ echo "{
  \"repository\":\"$REPOSITORY\",
  \"branch\":\"$BRANCH\",
  \"scope\":\"$SCOPE\"
}"

# {
#   "repository":"foo",
#   "branch":"bar",
#   "scope":"baz"
# }

```

## 環境

```sh
$ echo $BASH_VERSION
4.4.20(1)-release
```

## 方法

### 1. シングルクォートを使う

シングルクォートを使うと、JSON 中のダブルクォートをエスケープせずに済むため、上より見やすいです
ただし、そのままだと変数が展開されません。
そのため、変数の前後にシングルクォートを置き、文字列と変数を連結させることで、変数が展開できるようにします。
イメージ: `'~' + $BRANCH + '~'`

ただ、少し分かりにくいかもしれません。

```sh
# そのままだと変数が展開されない
$ echo '{
  "repository":"$REPOSITORY",
  "branch":"$BRANCH",
  "scope":"$SCOPE"
}'
# {
#   "repository":"$REPOSITORY",
#   "branch":"$BRANCH",
#   "scope":"$SCOPE"
# }

# 変数の前後にシングルクォートを置く
$ echo '{
  "repository":"'$REPOSITORY'",
  "branch":"'$BRANCH'",
  "scope":"'$SCOPE'"
}'

# {
#   "repository":"foo",
#   "branch":"bar",
#   "scope":"baz"
# }
```

### 2. jq コマンドを使う

[jq](https://stedolan.github.io/jq/)をインストールする必要があります。

`-n` オプションは入力値を無視する(null として扱う)オプションです。
通常、`jq`コマンドは入力値を必要としますが、`-n`を使うことで入力値無しに実行できます。詳細は[こちら](https://stedolan.github.io/jq/manual/)

また、jq コマンドを使用すると JSON が整形された状態で出力されるので、少し見やすいです。

またこちらの場合、変数に`"`や改行が組まれていた場合に自動にエスケープしてくれるので、JSON解析時のエラーになりづらいです。

```shell
$ jq -n \
 --arg repository "$REPOSITORY" \
 --arg branch "$BRANCH" \
 --arg scope "$SCOPE" \
  '{
    "repository":$repository,
    "branch":$branch,
    "scope":$scope,
  }'

# {
#   "repository":"foo",
#   "branch":"bar",
#   "scope":"baz"
# }

# 改行やダブルクォートを含めてみる
$ REPOSITORY='foo"
foo'

$ jq -n \
 --arg repository "$REPOSITORY" \
 --arg branch "$BRANCH" \
 --arg scope "$SCOPE" \
  '{
    "repository":$repository,
    "branch":$branch,
    "scope":$scope,
  }'

# {
#   "repository": "foo\"\nfoo",
#   "branch": "bar",
#   "scope": "baz"
# }
```

### 3. printf コマンドを使う

組み込みコマンドの[printf](https://www.atmarkit.co.jp/ait/articles/1907/05/news012.html)でも変数が埋め込み可能です。

```shell
$ printf '{
  "repository":"%s",
  "branch":"%s",
  "scope":"%s"
}
' $REPOSITORY $BRANCH $SCOPE

# {
#   "repository":"foo",
#   "branch":"bar",
#   "scope":"baz"
# }
```

## 参考

- [Build a JSON string with Bash variables - Stack Overflow](https://stackoverflow.com/questions/48470049/build-a-json-string-with-bash-variables)
- [shell - Expansion of variables inside single quotes in a command in Bash - Stack Overflow](https://stackoverflow.com/questions/13799789/expansion-of-variables-inside-single-quotes-in-a-command-in-bash)
- [【 printf 】コマンド――データを整形して表示する：Linux基本コマンドTips（319） - ＠IT](https://www.atmarkit.co.jp/ait/articles/1907/05/news012.html)
- [jqマニュアル（開発版）](https://stedolan.github.io/jq/manual/)
