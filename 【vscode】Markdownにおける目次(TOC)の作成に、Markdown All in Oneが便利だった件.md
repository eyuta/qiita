## はじめに

vscode にて、見出し (Heading) の内容に基づく目次(TOC, Table Of Contents)の作成方法を紹介します。

目次の作成には、[Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)という拡張機能の[Table of contents](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one#table-of-contents)を使います。
この目次は、見出しの変更に伴い自動で更新されます。

## デモ

- コマンドパレットで`Markdown All in One: Create Table of Contents`を実行すると、見出しの内容から目次が作成される
- 見出し更新後に`ctrl+s`で保存すると、目次も更新される

![vscode_toc_01.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/e170db4a-4ec8-5c45-80fd-ba1f2758a1b0.gif)

## インストール

1. `ctrl+p` -> `ext install Markdown All in One`
2. `Markdown All in One`をインストール

## 機能

### 目次の作成

`ctrl+shift+p` -> `Markdown All in One: Create Table of Contents`

### 目次の更新

- `ctrl+S`で保存
  - `markdown.extension.toc.updateOnSave`設定を切ることで、無効化も可能
- `ctrl+shift+p` -> `Markdown All in One: Update Table of Contents`

### 特定の見出しを目次から除外する

`<!-- omit in toc -->`を使います。
以下の場合、`## Table of Contents`が目次から除外されます。

```md
# Introduction

## Table of Contents <!-- omit in toc -->

- [Introduction](#introduction)
  - [Section 1](#section-1)

## Section 1
```

`markdown.extension.toc.omittedFromToc`設定を使い、User/Workspace 全体で一部の見出しを除外できます。

> ```json
> // In your settings.json
> "markdown.extension.toc.omittedFromToc": {
>   // Use a path relative to your workspace.
>   "README.md": [
>       "# Introduction",
>       "## Also omitted",
>   ],
>   // Or an absolute path for standalone files.
>   "/home/foo/Documents/todo-list.md": [
>     "## Shame list (I'll never do these)",
>   ]
> }
> ```

### セクション番号の追加/削除

目次を作成する際、見出しは一意である必要があります。
その際、セクション番号を使用すると、簡単に一意の見出しを用意できます。

`ctrl+shift+p` -> `Markdown All in One: Add/Update section numbers`

> ![section-numbers.gif](https://github.com/yzhang-gh/vscode-markdown/raw/master/images/gifs/section-numbers.gif)

見出しを追加した際も、上記のコマンドを実行することにより自動でセクション番号が更新されます。

## 参考

[Markdown All in One - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one#table-of-contents)
