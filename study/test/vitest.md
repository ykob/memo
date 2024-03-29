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

`vi`は`mock`や`spyOn`以外にも多数のAPIを内在している。

- [Vi | Vitest](https://vitest.dev/api/vi.html)

## テストの記述パターン

### テストのグループ化

`describe`を用いてテストをグループ化することができる。

```TypeScript
import { describe, expect, it } from 'vitest'

describe('テストのグループ化', () => {
  it('テスト1', () => {
    expect(true).toBe(true)
  })

  it('テスト2', () => {
    expect(true).toBe(true)
  })
})
```

### テストのイテレーション

`it.each`を用いてテストをイテレーションすることができる。  
https://vitest.dev/api/#test-each

```TypeScript
import { describe, expect, it } from 'vitest'

describe('テストのイテレーション', () => {
  test.each([
    [1, 1, 2],
    [1, 2, 3],
    [2, 1, 3],
  ])('add(%i, %i) -> %i', (a, b, expected) => {
    expect(a + b).toBe(expected)
  });
})
```

イテレーションの候補はテンプレートリテラルやオブジェクト形式で記述することもできる。

```TypeScript
test.each([
  { a: 1, b: 1, expected: 2 },
  { a: 1, b: 2, expected: 3 },
  { a: 2, b: 1, expected: 3 },
])('add($a, $b) -> $expected', ({ a, b, expected }) => {
  expect(a + b).toBe(expected)
});
```

```TypeScript
test.each`
  a    | b    | expected
  ${1} | ${1} | ${2}
  ${1} | ${2} | ${3}
  ${2} | ${1} | ${3}
`('add($a, $b) -> $expected', ({ a, b, expected }) => {
  expect(a + b).toBe(expected)
});
```

グループでも同様にイテレーションさせることができる。  
その場合は `describe.each` を用いる。  
https://vitest.dev/api/#describe-each

```TypeScript
import { describe, expect, it } from 'vitest'

describe.each([
  { a: 1, b: 1, expected: 2 },
  { a: 1, b: 2, expected: 3 },
  { a: 2, b: 1, expected: 3 },
])('describe object add($a, $b)', ({ a, b, expected }) => {
  test(`returns ${expected}`, () => {
    expect(a + b).toBe(expected)
  })

  test(`returned value not be greater than ${expected}`, () => {
    expect(a + b).not.toBeGreaterThan(expected)
  })

  test(`returned value not be less than ${expected}`, () => {
    expect(a + b).not.toBeLessThan(expected)
  })
})
```

#### イテレーションの候補の書き方

イテレーションの候補はなるべく静的に記述する。  
静的記述にするとコードは冗長になるが、どのような候補でテストしようとしているのかが一見で把握しやすくなるため。

以下のように記述する。

```TypeScript
it.each`
  hoge     | fuga
  ${true}  | ${true}
  ${true}  | ${false}
  ${false} | ${true}
  ${false} | ${false}
`('%s', (props) => {
　// expect
});
```

以下のような多重forを使った候補オブジェクトの生成は避ける。

```TypeScript
for (let i = 0; i < 2; i += 1) {
  for (let j = 0; j < 2; j += 1) {
    cases.push({
      hoge: i % 2 === 0,
      fuga: j % 2 === 0,
    });
  }
}
```

但し、あまりに候補の数が膨大になる場合はロジックによる候補の生成を選択してもよい。  
propsのフィールド数、propsに代入できる値の候補の数、それらの乗算の合計数で都度判断する。

