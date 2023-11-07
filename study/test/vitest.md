# Vitest

[Vitest | Next Generation testing framework](https://vitest.dev/)

Vitestは、Viteを基盤としたテスティングフレームワーク。  
ユニットテストのコードをJestライクに記述することができ、Jestよりも高速にテストを実行できることが特徴である。

## Install

VitestはVite本体を必要とするため、両方をインストールする必要がある。

```bash
npm install -D vite vitest
```

Vitestを実行するには`vitest`コマンドを利用する。  
`package.json`のnpm scriptsに記載すると、以下のようになる。

```json
{
  "scripts": {
    "test": "vitest"
  }
}
```

## Config

Vitestの設定は`vite.config.ts`に記述する。  
`test`フィールドにVitestの設定を記述する。

```TypeScript
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    // ...
  },
})
```

configでテスト対象のファイルを明記しなくても、テスト実行時に`**.test.{js,ts}`といったファイルを取得してくれる。  
testフィールドに記載できる内容については以下ドキュメントに記載がある。  

- [Configuring Vitest | Vitest](https://vitest.dev/config/)

### monorepo構成の場合

`vitest.workspace.ts`に複数のプロジェクトの設定をまとめて記述できる。

```TypeScript
import { defineWorkspace } from 'vitest/config';

export default defineWorkspace([
  {
    test: {
      name: 'web',
      root: './apps/web',
      environment: 'happy-dom',
    },
  },
  {
    test: {
      name: 'api',
      root: './apps/api',
      environment: 'node',
    },
  },
]);
```