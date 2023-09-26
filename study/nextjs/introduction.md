# Next.js の導入

## Next.js の主な特徴

https://nextjs.org/docs#main-features

- ルーティング: レイアウトシステム、ネストされたルーティング、読込状態、エラー処理に対応する
- レンダリング: サーバーサイドの静的/動的レンダリングに対応する
- データフェッチング: データフェッチの簡素化、リクエストのメモ化、データキャッシュ
- スタイリング: CSS モジュール、CSS-in-JS など多用な手法に対応する
- 最適化: 画像、フォント、script を最適化し、読込時間の高速化に対応する
- 型付け: TypeScript をサポート

## インストール

`npx create-next-app` コマンドを使う。

```
npx create-next-app [project-name]
```

## App Router と Pages Router の違い

2023 年 5 月、Next.js v13.4.0 で App Router の安定版が導入された。  
従来の Router は Pages Router と呼ばれるようになった。

### App Router の特徴

- Pages Router に比べディレクトリ構成が管理しやすくなった。
- 共通レイアウトの導入が以前に比べ容易になった。
- ページごとにメタデータを設定できる。
- コンポーネントはデフォルトで Server Components になっている。
- ルーティングフックには `next/navigation` を使う。
- データフェッチには独自のフックではなく `fetch` を使う。

### React Server Components (RSC) の特徴

- 多段階計算である。
- クライアントサイドで実行する React のコード (クライアントコンポーネント) をサーバーサイドで動的に生成する。 (サーバコンポーネント)
- PHP と似ている。
- クライアントコンポーネントから サーバコンポーネントを使用することはできない。
- クライアントコンポーネントの children として サーバコンポーネントを使用することはできる。
- コンポーネントをサーバコンポーネントにするか クライアントコンポーネントにするか迷ったときには、コンポーネントの役割を考えて判断する。
- サーバコンポーネントが実行されるタイミングは複数ある。
  - リクエスト時の情報に依存しないものであればビルド時に行う。(SG に似ている)
  - リクエスト時の情報に依存するものであればリクエスト時に行う。（SSR に似ている）
- 状態を持たないので、 `useState`, `useReducer` は使えない。 `useContext` も使えない。
- 再描画されないので、 `useEffefct`, `useLayoutEffect` は使えない。

## 参考リンク

- [Setup - Create a Next.js App | Learn Next.js](https://nextjs.org/learn/basics/create-nextjs-app/setup)
- [一言で理解する React Server Components](https://zenn.dev/uhyo/articles/react-server-components-multi-stage)
