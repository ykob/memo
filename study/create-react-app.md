# Create React App

## プロジェクト作成

以下コマンドを実行する

```
yarn create react-app my-app
```

### TypeScript

プロジェクト作成時にTypeScriptを含めてインストールする場合は `--template typescript` を付ける。

```
yarn create react-app my-app --template typescript
```

既成のプロジェクトにTypeScriptをインストールする場合は以下コマンドを実行する。  
この場合は当然、各ファイルのTypeScript対応は別途行う必要がある。（tsx化など）

```
yarn add typescript @types/node @types/react @types/react-dom @types/jest
```

### ESLint

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

## 参考リンク

- [新しい React アプリを作る](https://ja.reactjs.org/docs/create-a-new-react-app.html)
- [Create React App](https://create-react-app.dev/)
