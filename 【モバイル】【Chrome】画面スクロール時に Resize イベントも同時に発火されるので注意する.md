# 【モバイル】【Chrome】画面スクロール時に Resize イベントも同時に発火されるので注意する

## 結論

タイトルそのまま。

モバイル版の Chrome(Android, iPhone 問わず)では、画面をスクロールした際に resize イベントも同時に発火されるので、注意したい。
※Safari は不明。

## 挙動

挙動自体は Chrome の仕様。

スクロールした際、 Chrome の画面上部のアドレスバーの表示・非表示が切り替わる。
それにより Chrome の画面サイズが変更するため、Resize イベントが発行されるとのこと。
詳細は以下を参照。

### 参考

- <https://developers.google.com/web/updates/2016/12/url-bar-resizing>
- <https://stackoverflow.com/questions/17328742/mobile-chrome-fires-resize-event-on-scroll>
- [Viewport height is taller than the visible part of the document in some mobile browsers](https://nicolas-hoizey.com/articles/2015/02/18/viewport-height-is-taller-than-the-visible-part-of-the-document-in-some-mobile-browsers/)

## 対策

画面描画時に画面サイズを格納しておき、

```js
let width = window.innerWidth,
  height = window.innerHeight;
```

Resize イベント発火時に現在の画面サイズと発火前のそれを比較することで、画面サイズが変わった場合にのみイベントを発火させることができる。

```js
if (window.innerWidth !== width || window.innerHeight !== height) {
  //Do something
}
```

## 最後に

今回開発中の Web アプリにおいて、「モバイルを傾けた際に実行したい処理」を resize イベントに仕込んだところ、画面がスクロールされた際にも発火されてしまった。
以後気をつけたい。
