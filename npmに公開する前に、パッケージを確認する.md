## はじめに

`npm publish` で公開する前にパッケージを確認する方法として、以下の 2 つがありそうです。

- `npm link` で、開発中にパッケージの動作確認を行う
- `npm pack` で、`npm publish`で公開される内容を事前に確認する

それぞれ見ていきます。

## 環境

npm: 6.14.10

## 開発中にパッケージの動作確認を行う

npm-link を使うと、ローカルの別のパッケージから、確認したいパッケージを呼び出すことができます。

> npm link (in package dir)
> npm link [<@scope>/]<pkg>[@<version>]
>
> alias: npm ln

引用: [npm-link](https://docs.npmjs.com/cli/v6/commands/npm-link)

npm-link は次の 2 ステップで行います。

### 1. 参照したいパッケージで、`npm link`を実行する

参照したいパッケージで`npm link`を実行すると、グローバルな`node_modules`に、コマンドを実行したパッケージへのシンボリックリンクを作成します。

(グローバルな`node_modules`は、Linux だと`$(npm prefix -g)/lib/node_modules/$PACKAGE_NAME`になります)

(Windows だと`$(npm prefix -g)\node_modules\$PACKAGE_NAME`になりそうです)

また、パッケージ内の bin を `$(npm prefix -g)/bin/$PACKAGE_NAME` にリンクします。

参考: [npm-prefix](https://docs.npmjs.com/cli/v6/commands/npm-prefix)

### 2. 1.のパッケージを参照したい場所で`npm link $PACKAGE_NAME`を実行する

`npm link $PACKAGE_NAME`を実行すると、現在のフォルダの`node_modules`にグローバルな`node_modules/$PACKAGE_NAME`へのシンボリックリンクを作成します。

`$PACKAGE_NAME` は、ディレクトリ名ではなく、package.json から取得されることに注意します。

パッケージ名の前に[スコープ](https://docs.npmjs.com/cli/v6/using-npm/scope)を付けることができます。スコープは`@scopename/`で定義します。

### 例

例えば以下のような 2 つのパッケージがあるとします。

```tree
root
 ├─ some_module
 |  └─ node_modules
 └─ my_app
    └─ node_modules
```

`my_app`から`some_modules`を参照したい場合、以下のように実行します。

```sh
$ npm prefix -g # 確認用
# /usr
$ cd /root/some_module
$ npm link
# /usr/lib/node_modules/some_module -> /root/some_module
$ cd /root/my_app
$ npm link some_module
# /root/my_app/node_modules/some_module -> /usr/lib/node_modules/some_module -> /root/some_module
```

2 つのステップを一緒に行うこともできます。

```sh
$ cd /root/my_app
$ npm link ../some_module
# /root/my_app/node_modules/some_module -> /usr/lib/node_modules/some_module -> /root/some_module
```

この場合は、パッケージ名ではなく、ディレクトリ名を指定します。

## `npm publish`で公開される内容を事前に確認する

[npm-pack](https://docs.npmjs.com/cli/v6/commands/npm-pack)を使うと、`npm publish`で公開するものと同様のものが`.tgz`形式で出力されます。
これにより、公開前に出荷物を確できます。
(梱包される内容がログに吐き出されますので、分かりやすいです)

```sh
$ npm pack
# npm notice
# npm notice 📦  my_app@0.1.3
# npm notice === Tarball Contents ===
# npm notice 4.6kB dist/my-component.esm.js
# npm notice 5.2kB dist/my-component.min.js
# npm notice 5.5kB dist/my-component.umd.js
# npm notice 1.5kB package.json
# npm notice 6.2kB dist/my-component.esm.js.map
# npm notice 6.2kB dist/my-component.min.js.map
# npm notice 6.2kB dist/my-component.umd.js.map
# npm notice 310B  README.md
# npm notice === Tarball Details ===
# npm notice name:          my_app
# npm notice version:       0.1.3
# npm notice filename:      my_app-0.1.3.tgz
# npm notice package size:  5.2 kB
# npm notice unpacked size: 35.7 kB
# npm notice shasum:        d19a748b51e603fc385ec11134837428de10a0cc
# npm notice integrity:     sha512-8rxQlISLyS9zq[...]X9LBuapw7koLA==
# npm notice total files:   8
# npm notice
# my_app-0.1.3.tgz
```
