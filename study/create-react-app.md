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

## 参考リンク

- [新しい React アプリを作る](https://ja.reactjs.org/docs/create-a-new-react-app.html)
- [Create React App](https://create-react-app.dev/)
