# CSS

## React環境下におけるスタイリング手法

### CSS in JS

JavaScriptを用いてCSSを記述する方法。

#### 😀メリット

- スタイリングの変化をコンポーネントの状態に対応させやすい。
- pandaのように、ゼロランタイムで、テーマ機能やユーティリティ提供を含むといった、CSS in JSの弱点を補う機能を持つライブラリも登場している。

#### 😰デメリット

- Styled Componentは、コンポーネントをビューとロジックに明確に分離するため、ファイル数が増えすぎるおそれがある。
- ちょっとしたCSSの追加であってもそのためのコンポーネントが必要になる。
- ゼロランタイムでないライブラリがあり、RSCなどサーバ側で実行するJavaScriptと相性が悪いことがある。
  - ゼロランタイムとはビルド時にCSSを生成し、JSのランタイムで動的にCSSを生成しないもの。

#### 候補

- [Styled Components](https://styled-components.com/)
- [Emotion](https://emotion.sh/docs/introduction)
- [panda](https://panda-css.com/)

### Utility First

CSSライブラリが提供するクラスを使用してスタイリングする方法。

#### 😀メリット

- HTMLに直接、最小限の記述でスタイリングを付与できる。
- 要素を横並びにしたいときのFLexboxのように、セマンティクスを持たないHTML要素に対してのスタイリングを容易にできる。
- デザイントークンの構造とCSSクラス名の関連性を高く保ちやすい。

#### 😰デメリット

- 疑似要素、疑似クラス、Media Queries, Container Queriesなどの分岐が増えるほどHTMLの可読性を損ないやすい。
- 可読性の担保のためにTailwind CSSの`@apply`などのグループ化を使い出すと、Styled Componentとの差別化がしにくくなり、メリットを活かしきれない。

#### 候補

- [Tailwind CSS](https://tailwindcss.com/)

### CSS Modules

`*.modules.css` のように拡張子を定義したCSSファイルをJavaScriptから`import`してスタイリングを適用する方法。

#### 😀メリット

- CSS本来の記述方法が使えるので、学習コストが低い。
- Next.jsなどのフレームワークが標準搭載している。
- スコープが適用される。

#### 😰デメリット

- CSSは必ずJavaScriptとは別ファイルになる。
- いずれ非推奨になるおそれがある。
