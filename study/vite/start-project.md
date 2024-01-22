# Vite プロジェクト準備例

## 技術構成

- [pnpm](https://pnpm.io/ja/)
- [Vite](https://ja.vitejs.dev/)
- [TypeScript](https://www.typescriptlang.org/)
- [React](https://ja.legacy.reactjs.org/)
- [ESLint](https://eslint.org/) + [Prettier](https://prettier.io/)
- [Remix](https://remix.run/)
- [Panda CSS](https://panda-css.com/)
- [React Three Fiber](https://docs.pmnd.rs/react-three-fiber/getting-started/introduction)

## 各ツールのインストール

### pnpm

パッケージマネージャー。
インストールした`node_modules`の構成がnpm標準と異なり、モジュールディレクトリの直下ではなく階層別に配置されるようになる。  

- [pnpmのインストールコマンド一覧](https://pnpm.io/ja/installation)

### Vite

開発サーバー兼ビルドツール。  
以下`create`コマンドでテンプレート`react-ts`を用いることでReactとTypeScriptを併せて導入できる。

```powershell
pnpm create vite project-name --template react-ts
```

### TypeScript

JavaScriptのスーパーセットである静的型付け言語。  
上記Viteの`create`コマンドで同時にインストールされる。

### React

リアクティブなインターフェースを持つウェブアプリケーションを実現するJavaScriptフレームワーク。  
上記Viteの`create`コマンドで同時にインストールされる。

### ESLint

JavaScript, TypeScriptに対応した静的解析ツール。  
フォーマッターとしての機能も持つが、ここではフォーマットには別途Prettierを利用する。  
上記Viteの`create`コマンドで同時にインストールされる。

### Prettier

コードフォーマッター。  
上記Viteの`create`コマンドで同時にインストールされるが、設定ファイルや実行コマンドは生成されないため、準備する必要がある。(後述)

### Remix

Reactをベースとしたアプリケーションフレームワーク。  
Next.jsの対抗馬であり、Loader/Actionを用いてルートコンポーネントのサーバー/クライアント実行の切り分けをシンプルに行えるのが特徴。  
[PESPAs(Progressively Enhanced Single Page Apps)](https://www.epicweb.dev/the-webs-next-transition)というアーキテクチャに準拠しているらしい。

Remixのインストールと初期設定は以下Remix公式ドキュメントの内容に沿って行う。

- [Quick Start (5m) | Remix](https://remix.run/docs/en/main/start/quickstart)

またVite環境下におけるセットアップについても、以下Remix公式ドキュメントの内容に沿って行う。

- [Vite (Unstable) | Remix](https://remix.run/docs/en/main/future/vite)

### Panda CSS

ゼロランタイムで動作する静的型付け対応したCSSフレームワーク。  
オブジェクト形式でCSSを記述するが、デザインテーマやユーティリティといった機能も持ち、カスタマイズも出来る。

環境によって導入方法が異なるが、Viteと組み合わせる場合は以下公式ドキュメントの手順に従う。

- [Using Vite - Panda CSS](https://panda-css.com/docs/installation/vite)

Panda CSSはファイルを`styled-system`フォルダに生成する。  
このファイルは`panda codegen`コマンドを実行することによって生成されるので、出力元が同じソースであれば出力結果は同じになるはずで、Gitコミットやlintの対象からは除外しておく。
反対に、TypeScriptの型定義の対象には含めておく。

```json
# tsconfig.json
{
  "include":  ["src", "styled-system"]
}
```

Pandaで記述したCSSを適用する対象は`panda.config.ts`の`include`にパターンを追加する必要がある。  
Remixのソースコードは基本`src`ではなく`app`に配置することになるため、その場合は`include`のフォルダ名を修正しないとCSSが表示に適用されないので注意する。
