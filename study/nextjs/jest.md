# Next.js 環境下に Jest を導入する

## npm install

以下、必要最低限のモジュールをインストールする。

```
yarn add -D jest @testing-library/react @testing-library/jest-dom
```

## `jest.config.mjs`

`nextJest` を用いて jest の設定をラップする。  
jest に関する設定の項目は jest 公式にあるものと同様に追加できる。  
一部例として以下のものがある。

- `rootDir` : 実行対象のテストコードを含むディレクトリのパスを指定する。
- `testEnvironment` : テスト実行時に使用される環境を指定する。ブラウザライクな環境を使用するのであれば `jest-environment-jsdom` の指定でよい。

## テストコードを TypeScript に対応させる

`@types/jest` をインストールする。

```
yarn add -D @types/jest
```

## テスト実行用 npm コマンドを追加する。

package.json に以下を追加する。

```
{
  ...
  "scripts": {
    ...
    "test": "jest"
  },
  ...
}
```

- `jest` コマンドを実行すると、 `jest.config.mjs` の内容に従ってテストが実行される。
- `jest` に `--watch` オプションを付けると watch モードになる。

## 参考リンク

- [Optimizing: Testing | Next.js](https://nextjs.org/docs/pages/building-your-application/optimizing/testing)
- [Jest - Delightful JavaScript Testing](https://jestjs.io/ja/)
