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

## CSS の書き方

`<style jsx>` タグを React Component 内の JSX に追加し、その中身に CSS をテンプレートリテラルで記述する。

```
export default function () {
  return (
    <div>
      <Component className="component" />
      <style jsx>{`
        .component {
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
<style id="__jsx-1691720380">.component.jsx-1691720380{margin:1rem}</style>
```

`props` などによって CSS の値を動的にすることもできる。

```
export default function (props) {
  return (
    <div>
      <Component className="component" />
      <style jsx>{`
        .component {
          margin: ${props.margin || "1rem"};
        }
      `}</style>
    </div>
  );
}
```

CSS の値が変化したコンポーネントは、以下のようにレンダリング時にそれぞれにユニークな採番がされ異なる `style` タグで生成される。

```
<style id="__jsx-1691720380">.component.jsx-1691720380{margin:1rem}</style>
<style id="__jsx-382387474">.component.jsx-382387474{margin:2rem}</style>
```

## タグ付きテンプレートリテラル `css` を用いる

`css` という `styled-jsx` が提供するタグ付きテンプレートリテラルを用いて記述することもできる。  
CSS の記述を HTML 要素の JSX と分離させられるので、可読性は良さそう。

```
import css from "styled-jsx/css";

export default function () {
  const styles = css`
    .component {
      margin: 1rem;
    }
  `;

  return (
    <div>
      <Component className="component" />
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
    .component {
      margin: ${props.margin || "1rem"};
    }
  `;

  return (
    <div>
      <Component className="component" />
      <style jsx>{styles}</style>
    </div>
  );
}
```

`css` を利用しながら `props` などによってコンポーネントの見た目にバリエーションを持たせたい場合は、 `css` テンプレートリテラル内にバリエーション用の className を予め定義しておき、 `props` の値に応じて条件分岐で振り分けるようにすれば期待どおりに動作させられるので、

## 備考

- Create React App で作成したプロジェクトにも導入を試みたが、 `eject` の必要があるなど多少面倒そうだった。環境によって導入コストに大きく差がありそう。
