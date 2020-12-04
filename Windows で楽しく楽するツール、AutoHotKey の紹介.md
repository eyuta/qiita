# Windows で楽しく楽するツール、AutoHotKey の紹介
## はじめに

この記事は[「Develop fun!」を体現する Works Human Intelligence #2 Advent Calendar 2020](https://qiita.com/advent-calendar/2020/whi-2) 4 日目の記事となります。

「Develop fun!」ということで、Windows で楽しく開発する上で必須とも言えるツール、[AutoHotKey](https://www.autohotkey.com/)を紹介します。

## この記事で触れること・触れないこと

- [x] AutoHotKey の概要紹介
- [x] AutoHotKey でできること(の一部)の紹介
- [ ] インストール手順
  - 以下のサイトが詳しい
  - [[AutoHotKey]インストールと初期設定 – スタートアップに登録する方法](https://pouhon.net/ahk-install/661/)
- [ ] 開発環境の構築
  - [こちら](https://qiita.com/eyuta/items/d5d2e87a693d5f65924c)の記事で紹介。この記事で AutoHotkey に興味を持ったら Go!
- [ ] 各コマンドの詳細
  - [日本語ドキュメント](https://sites.google.com/site/autohotkeyjp/reference/commands)が充実

## AutoHotKey とは

```txt
AutoHotkeyはオープンソースで誰でも制限無く利用出来る※1 Windowsプラットフォームで動く強力なスクリプトエンジンで、
キーボードやマウスをカスタマイズしたり、ウィンドウ操作を自動化したりできます。

※1 : ライセンス形態は GNU General Public License
```

引用元: [AutoHotkeyJp](https://sites.google.com/site/autohotkeyjp/)

要するに、_スクリプトさえ書けば、キーボードやマウスで行う作業をほとんど自動化できます。_

## 実現できること

上でも紹介しましたが、[こちら](https://sites.google.com/site/autohotkeyjp/reference/Introduction)の日本語ドキュメントが詳しいです。
※英語版(公式)は[こちら](https://www.autohotkey.com/docs/AutoHotkey.htm)。
以下、筆者がよく使うもの書き出してます。

### リマップ

リマップ機能を利用すれば、特定のキー操作に別のキーを割り当てることが出来ます。
以下のようにスクリプトを書くだけで、Vim Like な矢印キー操作が可能になります。
※`#`は Windows キーを示す[修飾シンボル](https://sites.google.com/site/autohotkeyjp/reference/Hotkeys#TOC--)
※ `;`はコメントアウト

```ahk
#h::left    ;Win+H に ←キー を割り当て
#j::down    ;Win+J に ↓キー を割り当て
#k::up      ;Win+K に ↑キー を割り当て
#l::right   ;Win+L に →キー を割り当て
```

### プログラムの起動

好きなキーで好きなプログラムを起動することが出来ます。
起動時引数を渡すことも出来ます。

※`^`は Control キー
※`!`は Alt キー
※`<`、`>`は修飾シンボルの前につけることで、左右を限定する

```ahk
<^<!b::Run, C:\WINDOWS\system32\bash.exe           ;左Ctrl+左Alt+B で bashを起動
<^<!r::Run, c:\Program Files\clibor\Clibor.exe /ff ;;左Ctrl+左Alt+R で CliborをFIFOモードで起動
```

### スクリプトの実行

プログラムの起動だけでなく、スクリプトの実行もできます。
変数、関数、制御文といった基本的なことはできる認識です。
[ここ](https://sites.google.com/site/autohotkeyjp/reference/Scripts)に詳細が記述してあります。
ただ、AHK のスクリプトでゴリゴリ処理を書くよりは、トリガーを AHK にし、処理は別言語に移譲したほうがメンテナンスが簡単かな、と感じます。
AHK を業務で書くことはないので、文法をすぐ忘れてしまうのです。そのため、メンテナンスが大変に...

```ahk
<!+v:: ;コピーした文字列の先頭に`> `をつけて返す
    StrRefMsg := RegExReplace(Clipboard, "([^\n]+)", "> $1")
    Clipboard = %StrRefMsg%
    Send, ^v
    return

LShift up::
RShift up::
    ; 左 shift 空打ちで IME を OFF (一部スクリプト省略)
    if (A_PriorHotkey = "*~LShift")
    {
        IME_SET(0)
    }
    ; 右shift 空打ちで IME を ON
    if (A_PriorHotkey = "*~RShift")
    {
        IME_SET(1)
    }
    Return

;alt *2 で右クリック(ctrl + F10)
alt::
  KeyWait, alt
  if (A_PriorHotkey == A_ThisHotkey) && (300 > A_TimeSincePriorHotkey)
    send, +{F10}
  return
```

### ホットストリング

ホットストリングは、ユーザーが特定の文字列をタイプしたときにアクションを発生させられる機能です。
IME の辞書と同じような要領で使用できます。
個人的には、変換を挟まずに使える、`\t` や`\n` を受け付ける(入力中にフォーカス移動、改行、ボタンの押下が可能)といった理由で、此方のほうが好みです。
[こちらの記事](https://qiita.com/ysi/items/16436cd0cd90a138a36b)も参考になります。

```ahk
::mail::mail@gmail.com
::dept::XXXX Div.YYYY Dept.ZZZZ Grp.
::myuser::satoh{Tab}yut{Tab}phone1{Tab}address1{Enter} ;新規ユーザ作成

```

### その他

他にも、

- [ローマ字の再変換](https://yuruaki.blog.fc2.com/blog-entry-41.html)
- [任意のタイミングでの IME の切り替え](https://github.com/karakaram/alt-ime-ahk)
- [Virtual Desktop のカスタマイズ](https://github.com/sdias/win-10-virtual-desktop-enhancer)

等々、本当にいろんな事ができます。
(IME の切り替えはレジストリを変更せずに、自由に変換キーを設定できるので特におすすめ)

上記のスクリプトはそのまま exe ファイルととして実行もできますし、自身のスクリプトに関数として取り込むこともできます。
