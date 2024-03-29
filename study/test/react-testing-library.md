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

```shell
npm install --save-dev @testing-library/react
```

## `render(ui: ReactElement, options?)`

テストコード上で `ui` のレンダリング結果を得ることができる。  
例えばレンダリング結果に特定の文字列が含まれているかどうかを確認したい場合は、以下の処理と合わせてアサーションを定義することで実現できる。

- `screen` ... `@testing-library/dom` が提供するクエリで、スクリーン全体のDOMを参照する。
- `getByText(value)` ... `@testing-library/dom` が提供するメソッドで、 `scree` などにつなげて利用する。 `value` に代入した文字列を取得する。
- `toBeInTheDocument()` ... `jest-dom` の提供するマッチャー関数で、 `expect` の `value` に代入した要素がドキュメント内に存在するかどうかを判定する。

## Reactコンポーネントのテスト一例

- JSXに記載されたコンテンツの初期状態が想定どおりに表示されているか
- コンポーネント内のイベントの実行結果が想定どおりになっているか
- propsで渡した値が想定どおりに表示されているか
- propsで渡した関数がイベントによって想定どおりに実行されるか
- 条件分岐による表示の変更パターンがすべて想定どおりに表示されているか

## 気を付けたほうがいいこと

- 要素の取得にはアクセシブルネームを用いたほうがよい
  - `getByRole()` で `role` 引数だけでなく `option` 引数の `name` をきちんと指定する、 `getByLabelText()` を用いる、 `aria-label` や `aria-labelledby` といったWAI-ARIAの属性を用いるなど
  - アクセシブルネームを用いず `role` だけで判断しようとすると、誤った要素を取得してテストコードが想定どおりに行われない恐れが出る
  - [アクセシブルネームの算出には様々な要素が影響するので注意](https://www.w3.org/TR/accname-1.2/)
- テスト上での要素識別を適切に行うために、コンポーネントのJSXマークアップではアクセシビリティに配慮する

## スナップショットテスト

- ある時点のレンダリング結果をHTML文字列として外部ファイルに保存する
- `toMatchSnapshot`を含んだアサーションを実行することで行われる。
- 意図しないリグレッション（後戻り）を検知するのに役立つ。
- `<></>`で囲ったReactコンポーネントをスナップショットテストにかけるときには、要素の取得に`asFragment`を使うとよい。
  - [API | Testing Library](https://testing-library.com/docs/react-testing-library/api/#asfragment)

## Tips

### イベントの実行をテストする

`@testing-library/user-event` を用いると、実際のユーザー操作に近いシミュレートを行うことが出来る。  
例えば以下のように記述することで、ボタンをクリックしたときの挙動をテストすることが出来る。

```TypeScript
import userEvent from '@testing-library/user-event'

it('Click the submit button.', async () => {
  const user = userEvent.setup();

  render(<MyComponent />);
  const button = screen.getByRole('button', { name: "submit-button" });
  await user.click(button);
})
```

ユーザーイベントは非同期処理であるため、イベント着火前後のテスト順序を制御するために `await` を用いる必要がある点に注意する。  

### next/router のモック

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

## 参考リンク

- [React Testing Library | Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
- [WAI-ARIA ロール - アクセシビリティ | MDN](https://developer.mozilla.org/ja/docs/Web/Accessibility/ARIA/Roles)
