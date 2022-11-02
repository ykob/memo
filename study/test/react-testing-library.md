# React Testing Library

`React Testing Library` は React Component のテストを行うための API を提供するライブラリ。  
DOM ノードのテストを行うライブラリである `DOM Testing Library` の API を継承している。  
主に以下のような特徴がある。

- 非常に軽量である。
- [Jest](https://jestjs.io/ja/) と併用できる。

以下ライブラリも同時にインストールされ、併用することになる。

- `@testing-library/dom` ... DOMノードにアクセスできる API を追加する。 `screen`, `getBy...` など。
- `@testing-library/jest-dom` ... DOMノードにアクセスできるマッチャーを `jest` のAPIに追加する。 `toBeInTheDocument` など。

## インストール

以下コマンドで該当プロジェクトにインストールできる。

### npm

```
npm install --save-dev @testing-library/react
```

### yarn

```
yarn add --dev @testing-library/react
```

## `render(ui: ReactElement, options?)`

テストコード上で `ui` のレンダリング結果を得ることができる。  
例えばレンダリング結果に特定の文字列が含まれているかどうかを確認したい場合は、以下の処理と合わせてアサーションを定義することで実現できる。

- `screen` ... `@testing-library/dom` が提供するクエリで、スクリーン全体のDOMを参照する。
- `getByText(value)` ... `@testing-library/dom` が提供するメソッドで、 `scree` などにつなげて利用する。 `value` に代入した文字列を取得する。
- `toBeInTheDocument()` ... `jest-dom` の提供するマッチャー関数で、 `expect` の `value` に代入した要素がドキュメント内に存在するかどうかを判定する。

## next/router のモック

`jest.spyOn` を用いてモックする。  
具体的には以下のように記述する。

```
const useRouter = jest.spyOn(require("next/router"), "useRouter");
```

`useRouter` はモック関数なので `mockImplementation` などをつなげて実行できる。  
routerと同様のオブジェクトを代入することでrouterの挙動をテストコード上で制御することができる。

```
useRouter.mockImplementation(() => ({
  isReady: true,
  query: {},
}));
```

`require` を使用すると ESLint のルールによっては怒られることがあるが、根本的な回避策は今のところわかっていない。  
一般的に好ましい方法ではないが、 `// eslint-disable-next-line` を使うなどして回避する。


## 参考リンク

- [React Testing Library | Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
