## はじめに

記事を書く際、参考 URL をいちいちコピー・整形するのが面倒だったので、ブックマークレットで対応した。

ブックマークレットをクリックするだけで、ウェブページのリンクが Clipboard に書き込まれる。

## 結論

ブックマークレットに以下の URL を登録するだけ。

```js
javascript: navigator.clipboard.writeText(
  `[${document.title}](${location.href})`
);
```

例えば Qiita 上で上記のブックマークレットをクリックすると、`[eyuta - Qiita](https://qiita.com/eyuta)`のような文字列が Clipboard に書き込まれる。

後はこれを任意の場所に貼り付けるだけで良い。楽！

## 詳細

クリップボードへの格納には、[Clipboard.writeText() - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard/writeText) を使用している。

注意点としては、Web ページの`Document`にフォーカスが当たっていないと、以下のエラーが出力される点。
(アドレスバーやデベロッパーツールにフォーカスしている状態で実行すると、このエラーが出力される)

- `Uncaught (in promise) DOMException: Document is not focused.`

また、当該メソッドは非同期メソッドなので、後続処理を書く場合は`then`で繋ぐ必要がある

> ```js
> navigator.clipboard.writeText("<empty clipboard>").then(function() {
>   /* clipboard successfully set */
> }, function() {
>   /* clipboard write failed */
> });
> ```

参考:

- [Clipboard.writeText() - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard/writeText#example)
- [javascript-navigator.clipboard.readText（）の呼び出し時のDOMException-スタックオーバーフロー](https://stackoverflow.com/questions/56306153/domexception-on-calling-navigator-clipboard-readtext)

(上のブックマークレットでリンクを取得した)
