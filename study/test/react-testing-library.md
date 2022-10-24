# React Testing Library

`React Testing Library` は React Component のテストを行うための API を提供するライブラリ。  
DOM ノードのテストを行うライブラリである `DOM Testing Library` の API を継承している。  
主に以下のような特徴がある。

- 非常に軽量である。
- [Jest](https://jestjs.io/ja/) と併用できる。

以下ライブラリも同時にインストールされ、併用することになる。

- `@testing-library/jest-dom` ... DOMノードにアクセスできるマッチャーを `jest` のAPIに追加する。
- 

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

## 参考リンク

- [React Testing Library | Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
