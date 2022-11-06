# styled-jsx

Vercel が開発する CSS-in-JS ライブラリ。  
Next.js には標準で含まれている。

- [vercel/styled-jsx: Full CSS support for JSX without compromises](https://github.com/vercel/styled-jsx)
- [Blog - Styling Next.js with Styled JSX | Next.js](https://nextjs.org/blog/styling-next-with-styled-jsx)

以下の特徴がある。

- 通常の CSS と同じ書き方ができる。
- CSS の値を動的に変化させられる。
- CSS を容易にカプセル化できる。
- プラグインを用いれば SaSS や SCSS なども導入できる。

いずれも同様の候補が複数選べそうではあるが、 Next.js を用いる場合は導入の手間が省けることから他の選択肢よりも優位になるかもしれない。

## `styled-jsx` における基本的な CSS の書き方

`<style jsx>` タグを React Component 内の JSX に追加し、その中身に CSS をテンプレートリテラルで記述する。

```
export default function (props) {
  return (
    <div>
      { props.children }
      <style jsx>{`
        div {
          margin: 1rem;
        }
      `}</style>
    </div>
  );
}
```

このコンポーネントがレンダリングされる際に CSS は head タグ内の style タグとして生成される。  
ユニークな ID と className も自動付与され、これによって CSS のスコープも行われる。

```
<style id="__jsx-1691720380">div.jsx-1691720380{margin:1rem}</style>
```

`props` などによって CSS の値を動的にすることもできる。

```
export default function (props) {
  return (
    <div>
      { props.children }
      <style jsx>{`
        div {
          margin: ${props.margin || "1rem"};
        }
      `}</style>
    </div>
  );
}
```

CSS の値が変化したコンポーネントは、以下のようにレンダリング時にそれぞれにユニークな採番がされ異なる `style` タグで生成される。

```
<style id="__jsx-1691720380">div.jsx-1691720380{margin:1rem}</style>
<style id="__jsx-382387474">div.jsx-382387474{margin:2rem}</style>
```

また CSS のテンプレートリテラルを HTML の JSX の外に定義すると、レンダリング時に CSS のカプセル化がされない。

```
export default function (props) {
  const styles = `
    div {
      margin: 1rem;
    }
  `

  return (
    <div>
      { props.children }
      <style jsx>{styles}</style>
    </div>
  );
}
```

上記のように書くと、コンポーネントのレンダリング時に以下のような `jsx-` というプレフィクスが付かないグローバルな CSS が生成されてしまう。見た目上は一見 CSS が問題なく適用されているように見えることもあるが、この状態では `styled-jsx` を導入する利点が薄まるので、コンポーネント内の CSS の記述箇所には注意する。

```
<style type="text/css" data-styled-jsx="">
  div {
    margin: 1rem;
  }
</style>
```

## タグ付きテンプレートリテラル `css` を用いる

`css` という `styled-jsx` が提供するタグ付きテンプレートリテラルを用いて記述することもできる。  
上述した `css` を使わないテンプレートリテラルを用いた場合とは異なり、カプセル化も行われる。  
CSS の記述を HTML 要素の JSX と分離させられるので、可読性は良さそう。

```
import css from "styled-jsx/css";

export default function (props) {
  const styles = css`
    div {
      margin: 1rem;
    }
  `;

  return (
    <div>
      { props.children }
      <style jsx>{styles}</style>
    </div>
  );
}
```

ただし `css` を使う場合は、テンプレートリテラル内に変数を用いて CSS の値を動的に変化させることはできなくなる。  
以下の書き方ではレンダリング時に CSS が出力されない。

```
import css from "styled-jsx/css";

export default function (props) {
  const styles = css`
    div {
      margin: ${props.margin || "1rem"};
    }
  `;

  return (
    <div>
      { props.children }
      <style jsx>{styles}</style>
    </div>
  );
}
```

`css` を利用しながら `props` などによってコンポーネントの見た目にバリエーションを持たせたい場合は、 `css` テンプレートリテラル内にバリエーション用の className を予め定義しておき、 `props` の値に応じて条件分岐で振り分けるようにすれば期待どおりに動作させられる。

```
import css from "styled-jsx/css";

export default function (props) {
  const className = props.hasBgColor ? "red" : "white";
  const styles = css`
    div {
      margin: 1em;
    }
    div.white {
      background-color: white;
    }
    div.red {
      background-color: red;
    }
  `;

  return (
    <div className={className}>
      { props.children }
      <style jsx>{styles}</style>
    </div>
  );
}
```

ただしこの場合は以下のようにコンポーネントのレンダリング時に使用していない className もすべて含めた style タグが生成される。  
CSS の分岐パターン数が多くなる場合は `style` 内の文量が不必要にあふれるため、複雑な構造を持つコンポーネントの場合は（些細な差に過ぎないかもしれないが）この記述方法を避けるべきかもしれない。

```
<style id="__jsx-13a770607a2ac7ac">
div.jsx-13a770607a2ac7ac{margin:1rem}
div.white.jsx-13a770607a2ac7ac{background-color:white}
div.red.jsx-13a770607a2ac7ac{background-color:red}
</style>
```

## 親コンポーネントで定義した CSS を子コンポーネントに付与する

上述したやり方そのままで、親コンポーネントで定義した CSS テンプレートリテラルの className を子コンポーネントに付与するだけでは、親側で記述した CSS を子に適用させることはできない。親から子に `props` を用いて className の値を渡しても、子の要素の className には `jsx-13a770607a2ac7ac` というようなユニークな採番がされず CSS も適用されない。

```
const Component = function (props) {
  return <div className={props.className}>{props.children}</div>;
};

export default function () {
  return (
    <div>
      <Component className="child">This is a component.</Component>
      <style jsx>{`
        .child {
          margin-bottom: 1rem;
        }
      `}</style>
    </div>
  );
}
```

レンダリング時に出力された結果を見ると、`style` タグは生成されるが、子の HTML には className が付与されていないことがわかる。


```
<style id="__jsx-aadc64cd81b73338">.child.jsx-aadc64cd81b73338{margin-bottom:1rem}</style>

<div class="jsx-aadc64cd81b73338">
  <div class="child">This is a component.</div>
</div>
```

とはいえ、コンポーネントに付与したい CSS はすべて該当のコンポーネント内の記述のみに集中させるといったような設計では、実用の面で不便さが残る。  
外側からの CSS の付与によって元からコンポーネントが持っていた CSS のルールを闇雲に上書きして破壊するのは悪手なので避けるべきだが、そういった破壊的な意図による例を除外したとしても実際には親で定義した CSS を子に付与したいケースは頻出する。  
具体例の一つとして、一般的にコンポーネント内の最上位の要素に直接指定すべきではない `margin` の付与などが挙げられる。

このケースに対応するために `styled-jsx` では主に以下 2 つの手段が提供されている。

1. `css.resolve` を使う
2. `:global` を使う

### `css.resolve` を使う

`css.resolve` の使い方は公式ドキュメントを参照する。

- [The resolve tag](https://github.com/vercel/styled-jsx#the-resolve-tag)

`css.resolve` は `{ className: string; styles: JSX.Element }` という型のオブジェクトを返す。  
受け取った `className` はコンポーネントの className に渡し、 `styles` は **`style` タグで囲まずに**そのまま JSX 内に出力させる。

```
import css from "styled-jsx/css";

const Component = function (props) {
  return <div className={props.className}>{props.children}</div>;
};

export default function () {
  const cssResolve = css.resolve`
    .child {
      margin-bottom: 1rem;
    }
  `;

  return (
    <div>
      <Component className={`child ${cssResolve.className}`}>
        This is a component.
      </Component>
      {cssResolve.styles}
    </div>
  );
}
```

上記のようにコンポーネントを記述すると、レンダリング時に以下のように採番されたユニークな className を用いて `style` タグと子の HTML を出力してくれるので、親で定義した CSS を子に適用させることができるようになる。

```
<style id="__jsx-a43c8f37574f0aef">.child.jsx-a43c8f37574f0aef{margin-bottom:1rem}</style>

<div>
  <div class="child jsx-a43c8f37574f0aef">This is a component.</div>
</div>
```

### `:global` を使う

## 備考

- Create React App で作成したプロジェクトにも導入を試みたが、 `eject` の必要があるなど多少面倒そうだった。環境によって導入コストに大きく差がありそう。
