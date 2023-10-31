# Panda CSS

型安全に使用できるゼロランタイムのCSS-in-JSライブラリ。  
コンポーネントのためのCSSの記述だけでなく、テーマやデザイントークン、ユーティリティの定義も出来る。

- [Panda CSS](https://panda-css.com/)

## Conditional Styles

`css`関数を使ってオブジェクト形式でCSSを定義できる。  
疑似クラス、疑似要素へのCSSは`_hover`、`_focus`、`_before`、`_after`といったビルトインのフィールドを用いて指定する。  
メディアクエリによる分岐も同様に`sm`、`md`といったビルトインのフィールドを用いて定義する。

```
import { css } from 'styled-system/css'
 
const button = css({
  display: 'flex',
  flexDirection: 'row',
  justifyContent: 'center',
  alignItems: 'center',
  bg: 'red.600',
  _hover: {
    bg: 'red.900',
  }
});

const Button = ({ chirdren }) => (
  <button className={button}>
    { chirdren }
  </button>
)
```

## Patterns

Panda CSSは頻出するCSSパターンを標準で提供している。  

- `flex` ... Flexboxレイアウトに関係するCSSプロパティを一度に定義できる
- `grid` / `gridItem` ... Gridレイアウトに関係するCSSプロパティを一度に定義できる
- `visuallyHidden` ... Visually Hiddenを適用できる

パターンを使用することで、汎用的でセマンティックスを持たない要素に対して都度コンポーネントを定義したり、汎用コンポーネントを独自に定義する必要がなくなる。

## Recipe

`cva`関数を使ってpropsなどの値を元にした状態の変化を持つスタイルを定義できる。  

```
import { cva } from 'styled-system/css'
 
const button = cva({
  base: {
    display: 'flex',
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center'
  },
  variants: {
    size: {
      sm: { height: '24px', fontSize: '12px' },
      md: { height: '36px', fontSize: '14px' },
      lg: { height: '48px', fontSize: '16px' }
    }
  }
});

const Button = ({ chirdren }) => (
  <button className={button({ size: 'lg' })}>
    { chirdren }
  </button>
)
```

## Theme

`panda.config`上でCSSテーマを定義できる。  
テーマでは以下の項目を指定することができる。

- breakpoints ... ブレークポイント
- keyframes ... CSS Animationのkeyframe
- tokens ... デザイントークン（基礎的、原子的なルール）
- semanticTokens ... デザイントークン（セマンティックなルール）
- textStyles ... タイポグラフィ
- layerStyles ... ボックス要素を構成するスタイル（`background`, `border`, `color`, `padding`など）
- recipes ... [レシピ](https://panda-css.com/docs/concepts/recipes)
- slotRecipes ... [スロットレシピ](https://panda-css.com/docs/concepts/slot-recipes)
