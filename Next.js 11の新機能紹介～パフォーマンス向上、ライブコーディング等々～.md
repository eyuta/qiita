## はじめに

2021/6/16 に [Next.js 11](https://github.com/vercel/next.js/releases/tag/v11.0.0)がリリースされました。
本記事は、[こちらの Blog](https://nextjs.org/blog/next-11)を簡単にまとめたものになります。
(本文中の引用は、特に記述がなければ上記の Blog のものとなります)
(本文中の訳は、ほとんど Google 先生が頑張ってくれました :pray: )
アップグレードガイドは[こちら](https://nextjs.org/docs/upgrading)

## 主な変更

- コンフォーマンス(Conformance、適合性)
- パフォーマンスの向上
- `next/script`の最適化
- `next/image`の改善
- Webpack 5 のサポート
- Create React App からの移行 (実験的機能)
- Next.js Live (プレビューリリース)

## 詳細

### コンフォーマンス(Conformance、適合性)

> By leveraging a system of strong defaults and safeguards, they empower developers to focus more on features and products.
> (訳)強力なデフォルトとセーフガードのシステムを活用することで、開発者は機能や製品にさらに集中できるようになります。

参考: <https://nextjs.org/blog/next-11#conformance>

- [GoogleChrome とのコラボレーション](https://web.dev/conformance/#conformance-in-next.js)により、最適な UX と [Core Web Vitals](https://developers-jp.googleblog.com/2020/05/web-vitals.html) をサポートするためのソリューションとルールを提供する
- [プラグイン](https://www.npmjs.com/package/eslint-config-next)を含む [ESLint](https://eslint.org/) が最初からサポートされる
  - 新しくプロジェクトを作成した場合は、最初から`package.json`に`eslint`, `eslint-config-nexte`が含まれる
  - 既存のプロジェクトの場合は、Next.js 11 にアップグレードした後、`npx next lint` を実行する
- また、このコラボレーションには、Next.js 10 で追加された [Built-In Zero-Config TypeScript Support](https://nextjs.org/blog/next-9#built-in-zero-config-typescript-support) や [next/image](https://nextjs.org/docs/api-reference/next/image#width)も含まれる

### パフォーマンスの向上

> Since Next.js 10, we've been obsessed with further improving the developer experience of Next.js.
> (訳)Next.js 10 以降、Next.js の開発者エクスペリエンスをさらに向上させることに夢中になっています。

参考: <https://nextjs.org/blog/next-11#improved-performance>

- 起動時間を最大 24％改善
- React Fast Refresh を使用して変更の処理時間をさらに 40％短縮
- 起動時間をさらに短縮するための Babel の別の最適化が含まれる
  - webpack 用の Babel loader の新しい実装を作成し、読み込みを最適化し、メモリ内の構成キャッシュレイヤーを追加した

### `next/script`の最適化

> The new Next.js Script Component is a foundational optimization that enables developers to set the loading priority of third-party scripts to save developer time and improve loading performance.
> (訳) 新しい Next.js の スクリプトコンポーネントは、開発者がサードパーティスクリプトの読み込み優先度を設定して、開発者の時間を節約し、読み込みパフォーマンスを向上させることができます。

参考: <https://nextjs.org/blog/next-11#script-optimization>

`strategy`プロパティを使用することで、スクリプトの読み込みに自動的に優先順位を付けられる

- `beforeInteractive`: HTML に最初から挿入され、バンドルされた JavaScript が実行される前に実行される
  - ページがインタラクティブになる前に、フェッチして実行したいスクリプトに有用
    - 例: ボット検出、同意管理(consent management)
- `afterInteractive`(デフォルト): クライアント側に挿入され、ハイドレーション後に実行される
  - ページがインタラクティブになった後に、フェッチして実行したいスクリプトに有用
    - 例: タグマネージャーやアナリティクス
- `lazyOnload`: アイドル時間中にロードを待機できるスクリプトに有用
  - 例: チャットサポート、ソーシャルメディアウィジェット
- デフォルトのスクリプトの読み込み方法を、`async`から`defer`変更
  - サードパーティのスクリプトと、CSS、フォント、画像など、優先度の高いリソースとの競合を避けるため
  - 上記のプロパティを使い、必要に応じてスクリプトの優先順位を選択する

```jsx
// 例
<Script
  src="https://polyfill.io/v3/polyfill.min.js?features=Array.prototype.map"
  strategy="beforeInteractive" // lazyOnload, afterInteractive
  onLoad={() => {
    // If loaded successfully, then you can load other scripts in sequence
  }}
/>
```

### `next/image`の改善

> We're excited to share two of our community's top requested features for the next/image component, reducing Cumulative Layout Shift and creating a smoother visual experience.
> (訳) コミュニティで最も要望の多かった next/image コンポーネントの機能を 2 つ共有し、[Cumulative Layout Shift(累積レイアウトシフト)](https://blog.gaji.jp/2020/11/09/5509/)を減らし、よりスムーズな視覚体験を作成できます。

参考: <https://nextjs.org/blog/next-11#image-improvements>

#### 1. Automatic Size Detection (自動サイズ検出) (Local Images)

`import`を使用して画像の src をインポートすることで、自動的に`width`と`height`が定義される。

> ```jsx
> import Image from "next/image";
> import author from "../public/me.png";
>
> export default function Home() {
>   return (
>     // When importing the image as the source, you
>     // don't need to define `width` and `height`.
>     <Image src={author} alt="Picture of the author" />
>   );
> }
> ```

#### 2. Image Placeholders (画像プレースホルダー)

空白から画像への移行を容易にし、特にインターネット接続が遅いユーザーの場合、読み込み時間を短縮するために、ぼかしプレースホルダーをサポートする。

> ![Placeholder.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/58f0b773-4858-18e6-60e0-a14a69815dfd.gif)

ぼかしプレースホルダーを使うには、`placeholder="blur"`を Image に追加する。

> ```jsx
> <Image src={author} alt="Picture of the author" placeholder="blur" />
> ```

`jsblurDataURL`を使うことで、バックエンドによって提供されるカスタムを提供できるようにすることで、動的画像のぼかしも可能。

> ```jsx
> <Image
>   src="https://nextjs.org/static/images/learn.png"
>   blurDataURL="data:image/jpeg;base64,/9j/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcUFhYaHSUfGhsjHBYWICwgIyYnKSopGR8tMC0oMCUoKSj/2wBDAQcHBwoIChMKChMoGhYaKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCj/wAARCAAIAAoDASIAAhEBAxEB/8QAFQABAQAAAAAAAAAAAAAAAAAAAAb/xAAhEAACAQMDBQAAAAAAAAAAAAABAgMABAUGIWEREiMxUf/EABUBAQEAAAAAAAAAAAAAAAAAAAMF/8QAGhEAAgIDAAAAAAAAAAAAAAAAAAECEgMRkf/aAAwDAQACEQMRAD8AltJagyeH0AthI5xdrLcNM91BF5pX2HaH9bcfaSXWGaRmknyJckliyjqTzSlT54b6bk+h0R//2Q=="
>   alt="Picture of the author"
>   placeholder="blur"
> />
> ```

### Webpack 5 のサポート

Next.js 10.2 では、[Webpack 5](https://webpack.js.org/blog/2020-10-10-webpack-5-release/) のロールアウトを、`next.config.js`にカスタム Webpack 構成を含まないすべてのアプリケーションに拡張した。
今回、Next.js 11 で webpack 5 をすべての Next.js アプリケーションのデフォルトにする。

改善事項は[こちら](https://nextjs.org/blog/next-10-2#webpack-5)。
アップグレードドキュメントは[こちら](https://nextjs.org/docs/messages/webpack5)。

### Create React App からの移行 (実験的機能)

> To help developers convert their applications to Next.js, we've introduced a new tool to `@next/codemod` that automatically converts Create React App applications to be Next.js compatible.
> (訳) 開発者がアプリケーションを Next.js に`@next/codemod` 変換できるように、Create ReactApp アプリケーションを Next.js 互換に自動的に変換する[新しいツール](https://nextjs.org/docs/migrating/from-create-react-app)を導入しました。

Create React App プロジェクトの移行を開始するには、次のコマンドを使用します。

```shell
npx @next/codemod cra-to-next
```

### Next.js Live (プレビューリリース)

[Vercel の Web エディタを使用することで、ライブコーディングが可能になる。](https://nextjs.org/live)

![](https://nextjs.org/_next/image?url=%2Fstatic%2Fblog%2Fnext-11%2FBrowser.png&w=828&q=75)

リポジトリを指定するだけで、すぐに利用可能な模様。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/6950d03d-4b63-675e-eda7-cb64044b743b.png)

## 参考

- [Blog - Next.js 11 | Next.js](https://nextjs.org/blog/next-11)
- [eslint-config-next - npm](https://www.npmjs.com/package/eslint-config-next)
- [Conformance for Frameworks](https://web.dev/conformance/)
- [Google Developers Japan: Web Vitals の概要: サイトの健全性を示す重要指標](https://developers-jp.googleblog.com/2020/05/web-vitals.html)
- [Next.js Live - Code in the browser. With your team.](https://nextjs.org/live)
- [Introducing Aurora](https://web.dev/introducing-aurora/)
- [Next.js 11 Accelerates Frontend Performance and Enables Instant Collaboration to Let Developers Build the Next Big Thing, Faster | Business Wire](https://www.businesswire.com/news/home/20210615005365/en/Next.js-11-Accelerates-Frontend-Performance-and-Enables-Instant-Collaboration-to-Let-Developers-Build-the-Next-Big-Thing-Faster)
- [Next.js 11 now available with new live collaboration features - SD Times](https://sdtimes.com/webdev/next-js-11-now-available-with-new-live-collaboration-features/)
- [Upgrade Guide | Next.js](https://nextjs.org/docs/upgrading)
- [Releases · vercel/next.js](https://github.com/vercel/next.js/releases)
- [Google Developers Japan: Web Vitals の概要: サイトの健全性を示す重要指標](https://developers-jp.googleblog.com/2020/05/web-vitals.html)
- [Core Web Vitals の CLS(Cumulative Layout Shift) について ++ Gaji-Labo ブログ](https://blog.gaji.jp/2020/11/09/5509/)
