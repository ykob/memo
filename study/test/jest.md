# Jest

## モック関数

モック関数の役割については、公式に記載のとおり。

>  関数が持つ実際の実装を除去したり、関数の呼び出し（また、呼び出しに渡されたパラメータも含め）をキャプチャしたり、new によるコンストラクタ関数のインスタンス化をキャプチャできます。 そうすることでテスト時のみの返り値の設定をすることが可能になります。

単体テストを安定して行うためには外部サービスへの依存を避ける必要がある。  
モック関数を利用することでサービスに直接アクセスせずに同等の挙動を再現することができる。

### `jest.fn`

モック関数を返す。
`mockImplementation` を繋げることでモック関数の戻り値を指定することができる。  
また `jest.fn(implementation)` という書き方をしても `jest.fn().mockImplementation(implementation)` と同様の挙動になる。
さらに、引数を必要とせずに固定の値を返す関数を定義したい場合は `jest.fn().mockReturnValue` を利用することもできる。

```
// true を返すモック関数を作成する
const fn1 = jest.fn(() => true);
const fn2 = jest.fn().mockImplementation(() => true);
const fn3 = jest.fn().mockReturnValue(true);
```

`mockReturnValueOnce` を用いると、モック関数を呼び出す回数に応じて戻り値を変更することができる。

```
const fn = jest.fn()
  .mockReturnValueOnce(1)
  .mockReturnValueOnce(2)
  .mockReturnValue(0);

expect(fn).toBe(1);
expect(fn).toBe(2);
expect(fn).toBe(0);
```

### `jest.spyOn(object, methodName)`

`object[methodName]` のモック関数を返す。  
オブジェクトとそれが持つメソッド名を指定することで、そのモック関数を作成できる。
組み込み関数や独自のインスタンスなどを `object` に指定して利用する。

```
// TestModule インスタンスの methodName メソッドをモック関数にする
const instance = new TestModule();
const mockMethod = jest.spyOn(instance, 'methodName');

mockMethod.mockReturnValue(true);
```

例えば、乱数を返す組み込み関数の `Math.random()` を用いてその戻り値を指定したモック関数を作成したい場合は、以下のようにして実現できる。

```
const random = jest.spyOn(global.Math, 'random');

random.mockReturnValue(0.5);
```

## 参考リンク

- [Jest](https://jestjs.io/ja/)