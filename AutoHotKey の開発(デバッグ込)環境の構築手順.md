# AutoHotKey の開発(デバッグ込)環境の構築手順

## はじめに

この記事では、VSCode を用いて AutoHotkey の開発環境を整えた後、デバッグの仕方を軽く説明します。
AutoHotkey については、[こちら](https://qiita.com/eyuta/items/7fd4b536e58bfbdbf465)の記事で紹介していますので、よければ見てみてください。

## この記事で触れること

- [x] 他言語のように以下を行う
  - ブレイクポイントを用いたデバッグ
  - 任意のコードの実行
  - シンタックスハイライト
  - スクリプトのフォーマット
  - 関数の定義元・定義先へジャンプ
  - スクリプトのコンパイル(EXE ファイル化)

## 背景

興味のない方は読み飛ばしていただいて大丈夫です!!

従来、デバッグする際は、[この記事](https://www.autohotkey.com/docs/AHKL_DBGPClients.htm)や[この記事](https://qiita.com/kenichiro_ayaki/items/29a60514ff63f666e26a)に書いてあるように[DBGp](https://xdebug.org/docs/dbgp)というプロトコルを自分でエディタに組み込んでいました (しかし、対応しているエディタが少ないのが悩みどころ)。
また、上に挙げたような言語サポートも得ることができてませんでした。

しかし、2015 年に[AutoHotkey](https://marketplace.visualstudio.com/items?itemName=slevesque.vscode-autohotkey)、去年に[VSCode-Autohotkey-Plus](https://marketplace.visualstudio.com/items?itemName=cweijan.vscode-autohotkey-plus)がリリースされたことにより、皆様も使い慣れているであろう VS Code を用いて AutoHotkey の開発ができるようになりました。

## バージョン

- Windows: 10
- AutoHotkey: 1.1.33.02
- AutoHotkey(VSCode 拡張): 0.2.2
- VSCode-Autohotkey-Plus: 2.6.1

## 前提

- VSCode がインストール済み

## 構築手順

1. AutoHotkey をインストール
   - [公式](https://www.autohotkey.com/)から`Current Version`のインストーラをダウンロード後、インストールする。
   - [こちらのサイト](https://pouhon.net/ahk-install/661/)の解説が丁寧。
1. VSCode を起動
1. 以下の拡張機能をインストール
   - [AutoHotkey](https://marketplace.visualstudio.com/items?itemName=slevesque.vscode-autohotkey)
   - [VSCode-Autohotkey-Plus](https://marketplace.visualstudio.com/items?itemName=cweijan.vscode-autohotkey-plus)

これでおしまいです。

## 使い方

ほとんど上記の拡張機能の Overview に書いてあることをなぞる形になります。

### 準備

VScode を開き、新規の `.ahk`ファイルを作成します。
すると、最初から以下が記述されたファイルが作成されます。
これらは消しても良いですが、あると便利です。

```ahk
#SingleInstance, Force
SendMode Input
SetWorkingDir, %A_ScriptDir%
```

簡単に解説します。

- `#SingleInstance, Force`
  - [参考](http://ahkwiki.net/-SingleInstance)
  - 実行中のスクリプトがもうひとつ起動されたとき、自動的に既存のプロセスを終了して新たに実行開始する
  - これがないと、スクリプトを実行するたびに「既存のプロセスを終了して起動するか」聞かれることになる
- `SendMode Input`
  - [参考](http://ahkwiki.net/SendMode)
  - Send、SendRaw、Click コマンドおよび Mouse 系コマンドを処理する際に、システムに一連の操作イベントをまとめて送り込む
  - 他のモードだと、大量の Send コマンド等が発生したときに速度が遅くなるらしい
- `SetWorkingDir, %A_ScriptDir%`
  - [参考](http://ahkwiki.net/SetWorkingDir)
  - スクリプトの作業ディレクトリを実行スクリプトが置いてあるディレクトリにする
  - 作業ディレクトリは、相対パスを指定したときに基準となるディレクトリである
  - 基本はプログラムが実行された場所が作業ディレクトリになる

### デバッグ

他の言語と同じようにデバッグできます。

![a](https://github.com/cweijan/vscode-autohotkey/raw/master/image/debug.gif)
参照: [VSCode-Autohotkey-Plus](https://marketplace.visualstudio.com/items?itemName=cweijan.vscode-autohotkey-plus)

#### 機能

- 実行ボタンをクリック or F9 押下 or `AHK: Debug Script`コマンドでデバッグ開始
- ブレークポイント、スタックトレース、変数をサポート
- 出力メッセージ：デバッグ時に MsgBox の代わりに OutputDebug コマンドを使用すると、見やすくメッセージが表示される
- **注意** サブルーチン(`::`を含む命令)が途中に存在すると、その時点でデバッグが終わってしまう様子

  - なので、関数だけのファイルと、その関数を呼び出すファイルとに分けるとデバッグしやすい
  - before

    ```ahk
    <!+x::
    StrRefMsg := RegExReplace(Clipboard, "([^\n]+)", "> $1")
    Clipboard = %StrRefMsg%
    Send, ^v
    return
    ```
  - after

    ```main.ahk
    #Include, %A_WorkingDir%/func.ahk

    ;; CTRL + Xをクリックしたときに、>を挿入する
    <!+x::
      AddBlockquotes()
      Send, ^v
    return
    ```

    ```func.ahk
    AddBlockquotes()
    {
      StrRefMsg := RegExReplace(Clipboard, "([^\n]+)", "> $1")
      Clipboard = %StrRefMsg%
    }

    ```
