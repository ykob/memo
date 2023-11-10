# Vitest

[Vitest | Next Generation testing framework](https://vitest.dev/)

Vitestは、Viteを基盤としたテスティングフレームワーク。  
ユニットテストのコードをJestライクに記述することができ、Jestよりも高速にテストを実行できることが特徴である。

## インストール

VitestはVite本体を必要とするため、両方をインストールする必要がある。

```bash
npm install -D vite vitest
```

Vitestを実行するには`vitest run`コマンドを利用する。  
watch状態でVitestを実行するには`vitest`コマンドを利用する。  
`package.json`のnpm scriptsに記載すると、以下のようになる。

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
  }
}
```

## 設定

Vitestの設定は`vite.config.ts`に記述する。  
`test`フィールドにVitestの設定を記述する。

```TypeScript
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'happy-dom',
    globals: true,
    include: ['src/**/*.test.{ts,tsx}'],
  },
})
```

configでテスト対象のファイルを明記しなくても、テスト実行時に`**.test.{js,ts}`といったファイルを取得してくれる。  
testフィールドに記載できる内容については以下ドキュメントに記載がある。  

- [Configuring Vitest | Vitest](https://vitest.dev/config/)

Vitestと各種フレームワーク/ライブラリとの組み合わせのサンプルがGitHubにあるので、  
開発環境の構築時に参考に出来る。

- [vitest/examples at main · vitest-dev/vitest](https://github.com/vitest-dev/vitest/tree/main/examples)

## WorkSpaceの利用

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

当初はWorkspaceの利用がmonorepo構成に適しているかと思ったが、
プロジェクトごとにテストの作法や必要なモジュールは異なるので、
プロジェクトごとの`package.json`や`vite.config.ts`で管理するほうが結果シンプルに済んだ。

## React Testing Libraryと一緒に使う

以下Testing Libraryのモジュールをインストールする。

```bash
npm install -D @testing-library/react @testing-library/jest-dom
```

Vitestを利用する場合でも、テストコードでDOMを扱いたい場合は`@testing-library/jest-dom`を用いる。

## Jestとの違い

`afterEach`, `beforeEach`, `describe`, `expect`, `it`など、
頻繁に使用するAPIはJestと同様の形式で提供されており、Jestと区別なく使用できるようになっている。

モッキングについては、Jestでは`jest.mock()`もしくは`jest.spyOn()`を使用するが、  
Vitestでは`vi.mock()`もしくは`vi.spyOn()`を利用する。  

```TypeScript
let apples = 0
const cart = {
  getApples: () => 13,
}

const spy = vi.spyOn(cart, 'getApples').mockImplementation(() => apples)
apples = 1

expect(cart.getApples()).toBe(1)

expect(spy).toHaveBeenCalled()
expect(spy).toHaveReturnedWith(1)
```

`vi`は`mock`や`spyOn`以外にも多数のモック用APIを内在している。

- [Vi | Vitest](https://vitest.dev/api/vi.html)