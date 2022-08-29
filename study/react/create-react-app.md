# Create React App

## プロジェクト作成

以下コマンドを実行する

```
yarn create react-app my-app
```

## TypeScript

プロジェクト作成時にTypeScriptを含めてインストールする場合は `--template typescript` を付ける。

```
yarn create react-app my-app --template typescript
```

既成のプロジェクトにTypeScriptをインストールする場合は以下コマンドを実行する。  
この場合は当然、各ファイルのTypeScript対応は別途行う必要がある。（tsx化など）

```
yarn add typescript @types/node @types/react @types/react-dom @types/jest
```

## ESLint

ESLintは標準の構成に含まれているため、新規インストールは不要。  
`.eslintrc` は標準の構成に含まれておらず `package.json` 上に `eslintConfig` が存在している。設定を外部化する場合、 `npx eslint --init` を実するかファイルを別プロジェクトから複製するなど行して作成する必要がある。

### prettier

prettierは標準の構成に含まれていないので、使用する場合は関連モジュールをインストールする必要がある。

```
yarn add -D prettier eslint-config-prettier
```

prettierを実行するためのnpmスクリプトも追加する。

```
"scripts": {
  ...
  "format": "prettier --write . \"src/**/*.{js,jsx,ts,tsx}\""
},
```

## Tailwind CSS

1. 以下コマンドを実行して、必要なモジュールをインストールする。

```
yarn add -D tailwindcss postcss autoprefixer
```

2. Tailwind CSS の初期化を行う。

```
npx tailwindcss init -p
```

3. 2によって作成された `tailwind.config.js` の `content` に、Tailwind CSSを適用する対象を記述する。

```
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
}
```

4. `index.css` に `@tailwind` の記述を追加して、フレームワークを読み込ませる。

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Router

以下コマンドを実行して、必要なモジュールをインストールする。

```
yarn add react-router-dom
```

`v5` から `v6` にアップグレードした際に使えなくなったAPIがある。  
詳細は以下公式のmarkdownを参照。

- [Upgrade to React Router v6](https://github.com/remix-run/react-router/blob/main/docs/upgrading/v5.md#upgrade-to-react-router-v6)

## 参考リンク

- [新しい React アプリを作る](https://ja.reactjs.org/docs/create-a-new-react-app.html)
- [フックの導入 - React](https://ja.reactjs.org/docs/hooks-intro.html)
- [Create React App](https://create-react-app.dev/)
- [Install Tailwind CSS with Create React App](https://tailwindcss.com/docs/guides/create-react-app)
