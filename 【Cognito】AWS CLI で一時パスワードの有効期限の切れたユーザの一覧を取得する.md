# 【Cognito】AWS CLI で一時パスワードの有効期限の切れたユーザの一覧を取得する

## はじめに

パスワード再発行の挙動確認のため、一時パスワードの有効期限の切れたユーザを取得したかったので試行錯誤した件。

Cognito Console 上ではできなさそうだったので、AWS CLI を用いて取得した。

## 結論

```sh
aws cognito-idp list-users \
  --user-pool-id $YOUR_USER_POOL_ID \
  --output text \
  --query 'Users[? UserCreateDate<=`2021-01-22`
                   && UserStatus==`FORCE_CHANGE_PASSWORD`
                   && Enabled]
                .Attributes[] | [? Name==`email`].[Value] '
```

※UserCreateDate は、本日付から Cognito の User Pool に設定した一時パスワードの有効期限を引いた日付を指定する。

## 詳細

### `一時パスワードの有効期限の切れたユーザ`をどう表現するか

**NOTE**: AWS のドキュメントには、この情報は掲載されていなかったため、あくまでも推測と実際に一時パスワードの有効期限が切れていたユーザの情報から判断した。

#### 1. UserCreateDate<=`${本日付から Cognito の User Pool に設定した一時パスワードの有効期限を引いた日付}`

一時パスワードの有効期限は以下で確認できる。
[デフォルトは 7 日](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/user-pool-settings-admin-create-user-policy.html)。

```sh
aws cognito-idp describe-user-pool \
  --user-pool-id $YOUR_USER_POOL_ID \
  --query 'UserPool.Policies.PasswordPolicy.TemporaryPasswordValidityDays'
```

GUI から確認する場合は以下の通り。

1. [Amazon Cognito Console](https://ap-northeast-1.console.aws.amazon.com/cognito/home?region=ap-northeast-1)
2. `Manage User Pools`
3. `対象のUser Pool`をクリック
4. `General Settings` > `Policies`
5. `How quickly should temporary passwords set by administrators expire if not used?` の下の `Days to expire` を確認

#### 2. UserStatus==`FORCE_CHANGE_PASSWORD`

こちらは公式の[ユーザーアカウント確認の概要](https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/signing-up-users-in-your-app.html#signup-confirmation-verification-overview)に記載がある。

> ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/e3918f1e-b28e-8c94-f581-d72f78a09731.png)

> Force Change Password
> The user account is confirmed and the user can sign in using a temporary password, but on first sign-in, the user must change his or her password to a new value before doing anything else.
>
> User accounts that are created by an administrator or developer start in this state.

#### 3. Enabled

上の図にある通り、Disabled なユーザはそもそもパスワードの再発行ができないので、今回は除外する。
ちなみに、ユーザの `Enabled /Disabled` は 2.の UserStatus では管理しておらず、`Enabled`という Boolean の項目で管理されている。

参考: [list-users | Output](https://docs.aws.amazon.com/cli/latest/reference/cognito-idp/list-users.html#output)

### 上記をどう Query (JMESPath) で表現するか

必要な情報は出揃ったので、上の情報を query に落とし込む。
[ここ](https://docs.aws.amazon.com/cli/latest/reference/cognito-idp/list-users.html#output)にあるように、`list-users`は以下の形式で出力される。

尚、JMESPath は[ここ](https://jmespath.org/)で試せるため、ここで検証すると楽。

```json
{
  "Users": [
    {
      "Username": "22704aa3-fc10-479a-97eb-2af5806bd327",
      "Enabled": true,
      "UserStatus": "FORCE_CHANGE_PASSWORD",
      "UserCreateDate": "2020-12-25T13:24:26.818000+09:00",
      "UserLastModifiedDate": "2020-12-25T13:33:37.450000+09:00",
      "Attributes": [
        {
          "Name": "sub",
          "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
        },
        {
          "Name": "email_verified",
          "Value": "true"
        },
        {
          "Name": "email",
          "Value": "mary@example.com"
        }
      ]
    },
    {
      "Username": "22704aa3-fc10-479a-97eb-2af5806bd327",
      "Enabled": true,
      "UserStatus": "FORCE_CHANGE_PASSWORD",
      "UserCreateDate": "2020-12-25T13:24:26.818000+09:00",
      "UserLastModifiedDate": "2020-12-25T13:33:37.450000+09:00",
      "Attributes": [
        {
          "Name": "sub",
          "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
        },
        {
          "Name": "email_verified",
          "Value": "true"
        },
        {
          "Name": "email",
          "Value": "mary@example.com"
        }
      ]
    },
    {
      "Username": "22704aa3-fc10-479a-97eb-2af5806bd327",
      "Enabled": false,
      "UserStatus": "FORCE_CHANGE_PASSWORD",
      "UserCreateDate": "2020-12-25T13:24:26.818000+09:00",
      "UserLastModifiedDate": "2020-12-25T13:33:37.450000+09:00",
      "Attributes": [
        {
          "Name": "sub",
          "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
        },
        {
          "Name": "email_verified",
          "Value": "true"
        },
        {
          "Name": "email",
          "Value": "mary@example.com"
        }
      ]
    }
  ]
}
```

#### 1. ユーザの絞り込み

最初に Users を絞り込みたい。絞り込みは、[Filter Expression](https://jmespath.org/specification.html#filter-expressions)を使用する。
Filter Expression は、`[? ${任意の式}]`で表現される式である。
`任意の式`には各種演算子(`==`, '<=', etc)が使える。
今回の例でいうと、`任意の式`に以下の 1 つの式を入れる必要がある。

- UserCreateDate<=`2021-01-22`
- UserStatus==`FORCE_CHANGE_PASSWORD`
- Enabled
  - Enabled は真偽値なので、単体で OK

AND 条件を使いたい場合は、[And Expression](https://jmespath.org/specification.html#and-expressions)を使う。

まとめると、

```sh
Users[? UserCreateDate<=`2021-01-22`
      && UserStatus==`FORCE_CHANGE_PASSWORD`
      && Enabled]
```

でユーザを絞り込める。
絞り込んだ結果は以下の通り。
`"Enabled": true,`のデータのみ絞り込まれる。

```json
[
  {
    "Username": "22704aa3-fc10-479a-97eb-2af5806bd327",
    "Enabled": true,
    "UserStatus": "FORCE_CHANGE_PASSWORD",
    "UserCreateDate": "2020-12-25T13:24:26.818000+09:00",
    "UserLastModifiedDate": "2020-12-25T13:33:37.450000+09:00",
    "Attributes": [
      {
        "Name": "sub",
        "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
      },
      {
        "Name": "email_verified",
        "Value": "true"
      },
      {
        "Name": "email",
        "Value": "mary@example.com"
      }
    ]
  },
  {
    "Username": "22704aa3-fc10-479a-97eb-2af5806bd327",
    "Enabled": true,
    "UserStatus": "FORCE_CHANGE_PASSWORD",
    "UserCreateDate": "2020-12-25T13:24:26.818000+09:00",
    "UserLastModifiedDate": "2020-12-25T13:33:37.450000+09:00",
    "Attributes": [
      {
        "Name": "sub",
        "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
      },
      {
        "Name": "email_verified",
        "Value": "true"
      },
      {
        "Name": "email",
        "Value": "mary@example.com"
      }
    ]
  }
]
```

#### 2. email の出力

この時点で欲しかった情報は得られたが、ここから一歩踏み込んで、絞り込まれたユーザの email のみを出力するようにする。
まず、絞り込んだユーザの`Attributes`のみを出力する。

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes
```

```json
[
  [
    {
      "Name": "sub",
      "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
    },
    {
      "Name": "email_verified",
      "Value": "true"
    },
    {
      "Name": "email",
      "Value": "mary@example.com"
    }
  ],
  [
    {
      "Name": "sub",
      "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
    },
    {
      "Name": "email_verified",
      "Value": "true"
    },
    {
      "Name": "email",
      "Value": "mary@example.com"
    }
  ]
]
```

出力された Attributes のうち、email のみを取得したい。
email のみ、は`[? Name=='email']`で表現できる。
ただ、現状だと二重配列になっており、オブジェクト要素にアクセスできないため、[フラット演算子](https://jmespath.org/specification.html#flatten-operator)を使って配列をフラットにする。
フラット演算子は、添字(1,2,3...)を使うとその添字の値のみを取得してフラットな配列を作るが、添字の無い空配列を使うと全件取得する。

##### 空配列

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes[]
```

各ユーザの 全ての Attributes を取得する。

```json
[
  {
    "Name": "sub",
    "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
  },
  {
    "Name": "email_verified",
    "Value": "true"
  },
  {
    "Name": "email",
    "Value": "mary@example.com"
  },
  {
    "Name": "sub",
    "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
  },
  {
    "Name": "email_verified",
    "Value": "true"
  },
  {
    "Name": "email",
    "Value": "mary@example.com"
  }
]
```

##### 添字 == 0 (1 番目)

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes[0]
```

各ユーザの Attributes のうち、1 番目の要素を取得する。

```json
[
  {
    "Name": "sub",
    "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
  },
  {
    "Name": "sub",
    "Value": "22704aa3-fc10-479a-97eb-2af5806bd327"
  }
]
```

##### 添字 == 2 (3 番目)

今回の場合、`"Name": "email"`な要素は 3 番目にあるため、添字に 2 を指定することで email だけを抽出することは可能である。
ただし、今後 Attributes が増えた際に困るので、今回は空配列`[]`を用いることにする。

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes[2]
```

各ユーザの Attributes のうち、1 番目の要素を取得する。

```json
[
  {
    "Name": "email",
    "Value": "mary@example.com"
  },
  {
    "Name": "email",
    "Value": "mary@example.com"
  }
]
```

次に、`[? Name=='email']`で絞り込みたい。
しかし、素直に Filter Expression を使っても、期待した結果が返ってこない。

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes[][? Name==`email`]
```

```json
[]
```

これは、投影(Projection)と呼ばれる現状です。
[Wildcard Expressions](https://jmespath.org/specification.html#wildcard-expressions)には以下のようにあります。

> The [*] syntax (referred to as a list wildcard expression) will return all the elements in a list. Any subsequent expressions will be evaluated against each individual element. Given an expression [*].child-expr, and a list of N elements, the evaluation of this expression would be [child-expr(el-0), child-expr(el-2), ..., child-expr(el-N)]. This is referred to as a projection, and the child-expr expression is projected onto the elements of the resulting list.
>
> Once a projection has been created, all subsequent expressions are projected onto the resulting list.
>
> The \* syntax (referred to as a hash wildcard expression) will return a list of the hash element’s values. Any subsequent expression will be evaluated against each individual element in the list (this is also referred to as a projection).

簡単にいうと、`任意の条件で絞り込んだ場合、後続の式は絞り込んだ要素に対して適用`する。
今回の場合、`User[? ~~]`や`[]`を用いて絞り込みを行っている状態なので、後続の式 (今回であれば` | [? Name=='email']`) は絞り込まれた各要素である `{"Name": XX, "Value": YY}`に対して適用される。

つまり、

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes[].Name
```

とすると、各要素の Name プロパティが出力される形となる。

```json
["sub", "email_verified", "email", "sub", "email_verified", "email"]
```

この投影状態を無視し、出力されたオブジェクト全体に対して絞り込みを行いたい場合、[Pipe Expressions](https://jmespath.org/specification.html#pipe-expressions)を使用する。

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes[] | [? Name==`email`]
```

```json
[
  {
    "Name": "email",
    "Value": "mary@example.com"
  },
  {
    "Name": "email",
    "Value": "mary@example.com"
  }
]
```

ようやく email 要素だけを絞り込むことができた。

ここらへんの挙動について以下の記事にまとめた見たので読んでいただけると嬉しい。

[【JMESPath】`foo[*][0]` と`foo[*] | [0]` の違い](https://qiita.com/eyuta/items/05676248b04f74a25670)

さて、この状態も各要素が投影されている状態なので、`.Value`で各 Value 要素だけを絞り込むことができる。

```sh
Users[? UserCreateDate<=`2021-01-22`
     && UserStatus==`FORCE_CHANGE_PASSWORD`
     && Enabled]
.Attributes[] | [? Name==`email`].Value
```

```json
["mary@example.com", "mary@example.com"]
```

### 出力方法

最後に出力方法を変更する。
AWS CLI は、デフォルトで JSON 形式で出力される。しかし、今回の場合は`"`も`[]`も無い純粋なテキストデータがほしい。
その場合、`--output text`オプションを用いると、出力がテキスト形式になる。

```sh
aws cognito-idp list-users \
  --user-pool-id $YOUR_USER_POOL_ID \
  --output text \
  --query 'Users[? UserCreateDate<=`2021-01-22`
                   && UserStatus==`FORCE_CHANGE_PASSWORD`
                   && Enabled]
                .Attributes[] | [? Name==`email`].Value'
```

```txt
mary@example.com       mary@example.com
```

……。

これは AWS CLI の仕様になります。

[テキストの出力形式](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-usage-output.html#text-output)のヒントには以下のようにある。

> テキスト出力を行い、--query パラメータを使用して出力を単一のフィールドにフィルタリングすると、出力は 1 行のタブ区切り値になります。次の例に示すように、各値を別々の行に入れるには、出力フィールドを角括弧で囲みます。

つまり、1 つの配列内の要素はタブ区切りで 1 行で表示されてしまうようです。
これの解決策は、query の最後で指定した`Value`を括弧で括れば良い。

```sh
aws cognito-idp list-users \
  --user-pool-id $YOUR_USER_POOL_ID \
  --output text \
  --query 'Users[? UserCreateDate<=`2021-01-22`
                   && UserStatus==`FORCE_CHANGE_PASSWORD`
                   && Enabled]
                .Attributes[] | [? Name==`email`].[Value]'
```

```txt
mary@example.com
mary@example.com
```

JSON だとこのように出力されている。

```json
[["mary@example.com"], ["mary@example.com"]]
```

### 蛇足

重複を排除したい場合は、JMESPath では対応できないので、[`uniq` コマンド](https://www.atmarkit.co.jp/ait/articles/1611/14/news021.html)を使用する。

```sh
aws cognito-idp list-users \
  --user-pool-id $YOUR_USER_POOL_ID \
  --output text \
  --query 'Users[? UserCreateDate<=`2021-01-22`
                   && UserStatus==`FORCE_CHANGE_PASSWORD`
                   && Enabled]
                .Attributes[] | [? Name==`email`].[Value]' \
  | sort | uniq
```

```txt
mary@example.com
```

## 参考

- [Text output format](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-output.html#text-output)
- [Index Expressions | JMESPath Specification](https://jmespath.org/specification.html#index-expressions)
- [JMESPath で配列から要素を抽出するにはパイプを用いる](https://qiita.com/cold-wisteria/items/15f0369544b9de5d3952)
  "
