# Jest

## よく使うAPI

### `jest.fn`

モック関数を返す。
単体テストではテストを安定して行うために外部サービスへの依存を避ける必要がある。  
モック関数を利用することでサービスに直接アクセスせずに同等の挙動を再現することができる。

`mockImplementation` を繋げることでモック関数の戻り値を指定することができる。  
また `jest.fn(implementation)` という書き方をしても `jest.fn().mockImplementation(implementation)` と同様の挙動になる。
さらに、引数を必要とせずに固定の値を返す関数を定義したい場合は `jest.fn().mockReturnValue` を利用することもできる。

```
// true を返すモック関数
const fn1 = jest.fn(() => true);
const fn2 = jest.fn().mockImplementation(() => true);
const fn3 = jest.fn().mockReturnValue(true);
```

## 参考リンク

- [Jest](https://jestjs.io/ja/)