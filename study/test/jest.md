# Jest

Jest は Meta 社により開発された JavaScript テスティングフレームワークである。  
シンプル・快適さを重視しており、 zero config ですぐに利用できる。  
`DOM Testing Library` などの他のテスト用ライブラリと組み合わせて使用することもできる。

## インストール

インストールコマンドは以下のとおり。

### npm

```
npm install --save-dev jest
```

### yarn

```
yarn add --dev jest
```

## アサーションとマッチャー

アサーションとは、記述した処理の応答が正しいものかどうかをチェックすること。  
Jestではアサーションに `expect` を用いる。

- [Expect · Jest](https://jestjs.io/ja/docs/expect)

`expect` オブジェクトにはマッチャー `Matcher` と呼ばれるメソッドが多数存在する。  
マッチャーは値の検証方法を示すメソッドであり、マッチャーを用いることで `expect` の第一引数に渡した値が意図した値になっているかどうかを確認することができる。

`expect` を用いて値を検証する単純な例は以下のようになる。

```
// value は true である。
expect(value).toBe(true);
```

比較的よく用いるマッチャーメソッドには以下のようなものがある。

|マッチャー|意味|
|---|---|
|`toBe(value)`|`expect` に渡した値と `value` を比較する。<br>浮動小数点数を用いる場合は代わりに `toBeCloseTo` を利用する。|
|`toBeNull()`|指定の値が `null` の場合に一致する|
|`toBeUndefined()`|指定の値が `undefined` の場合に一致する|
|`toBeDefined()`|指定の値が `undefined` ではない場合に一致する|
|`toBeTruthy()`|指定の値が `true` の場合に一致する|
|`toBeFalsy()`|指定の値が `false` の場合に一致する|
|`toHaveBeenCalled()`|指定のモック関数が呼び出されたかどうかを確認する。|
|`toThrow(error?)`|指定の関数がエラーを投げたかどうか確認する。引数に正規表現、文字列、エラーオブジェクト、エラークラスを指定すると、エラーがそれに一致するかどうかまで確認できる。|

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

### `jest.mock(moduleName, factory, options)`

`moduleName` で指定したモジュールが `require` あるいは `import` された際に、そのモックを自動的に作成する。  


## 参考リンク

- [Jest](https://jestjs.io/ja/)