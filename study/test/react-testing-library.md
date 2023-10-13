# React Testing Library

`React Testing Library` は React Component のテストを行うための API を提供するライブラリ。  
DOM ノードのテストを行うライブラリである `DOM Testing Library` の API を継承している。  
主に以下のような特徴がある。

- 非常に軽量である。
- [Jest](https://jestjs.io/ja/) と併用できる。

以下ライブラリも同時にインストールされ、併用することになる。

- `@testing-library/dom` ... DOMノードにアクセスできる API を追加する。 `screen`, `getBy...` など。

必要に応じて以下ライブラリもインストールする。

- `@testing-library/jest-dom` ... DOMノードにアクセスできるマッチャーを `jest` のAPIに追加する。 `toBeInTheDocument` など。
- `@testing-library/user-event` ... `@testing-library/jest-dom` よりも実際のユーザー操作に近いシミュレートを行うことが出来る。

## インストール

以下コマンドで該当プロジェクトにインストールできる。

### npm

```Shell
npm install --save-dev @testing-library/react
```

### yarn

```Shell
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
具体的には以下のように記述してモック関数を定義する。

```TypeScript
const useRouter = jest.spyOn(require("next/router"), "useRouter");
```

ここで定義された変数 `useRouter` は jest のモック関数なので `mockImplementation` などをつなげて実行できる。  
routerと同様のオブジェクトを代入することでrouterの挙動をテストコード上で制御することができる。

```TypeScript
useRouter.mockImplementation(() => ({
  isReady: true,
  query: {},
}));
```

`require` を使用すると ESLint のルールによっては怒られることがあるが、根本的な回避策は今のところわかっていない。  
一般的に好ましい方法ではないが、 `// eslint-disable-next-line` を使うなどして回避する。

## Reactコンポーネントのテスト一例

- JSXに記載されたコンテンツの初期状態が想定どおりに表示されているか
- コンポーネント内のイベントの実行結果が想定どおりになっているか
- propsで渡した値が想定どおりに表示されているか
- propsで渡した関数がイベントによって想定どおりに実行されるか
- 条件分岐による表示の変更パターンがすべて想定どおりに表示されているか

## 気を付けたほうがいいこと

- 要素の取得にはアクセシブルネームを用いたほうがよい
- テスト上での要素識別を適切に行うために、コンポーネントのJSXマークアップではアクセシビリティに配慮する

## 参考リンク

- [React Testing Library | Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
