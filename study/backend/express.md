# express

expressはNode.js用のウェブアプリケーションフレームワーク。  
https://expressjs.com/

expressは非常に軽微なフレームワークであり、基本的な機能はルーティングとミドルウェアの2つのみである。

## ルーティング

ルーティングは`app.METHOD(PATH, HANDLER)`の形式で定義する。  
METHODはHTTPメソッド、PATHはサーバー上のパス、HANDLERはルートが一致したときに実行される関数を指す。

```typescript
const app = express();

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.post('/', (req, res) => {
  res.send('Got a POST request');
});

app.put('/user', (req, res) => {
  res.send('Got a PUT request at /user');
});

app.delete('/user', (req, res) => {
  res.send('Got a DELETE request at /user');
});
```

ExpressはすべてのHTTPリクエストメソッドに対応するメソッドをサポートしている。  
https://expressjs.com/ja/4x/api.html#app.METHOD

### ルート・パラメータ

ルート・パラメータは、ルート・パスの一部として指定された名前付き変数である。  
`:`を使ってルートを定義すると、その値を変数として受け取ることができる。

```typescript
app.get('/users/:userId/books/:bookId', (req, res) => {
  res.send(req.params);
  console.log(req.params);
});

// http://localhost:3000/users/34/books/8989
// { "userId": "34", "bookId": "8989" }
```

### ルート・ハンドラ

ルート・ハンドラは、ミドルウェアのように動作する1つまたは複数のコールバック関数を含む関数である。
以下の例では1つのコールバック関数で1つのルートを処理する。

```typescript
app.get('/example/a', (req, res) => {
  res.send('Hello from A!')
})
```

以下の例では、2つのコールバック関数で1つのルートを処理する。

```typescript
app.get(
  '/example/b',
  (req, res, next) => {
    console.log('the response will be sent by the next function ...');
    next();
  },
  (req, res) => {
    res.send('Hello from B!');
  }
);
```

コールバック関数を配列として渡して1つのルートを処理することもできる。
コールバック関数を配列として渡すと、配列内のコールバック関数が順番に呼び出される。

```typescript
const cb0 = (req, res, next) => {
  console.log('CB0');
  next();
};

const cb1 = (req, res, next) => {
  console.log('CB1');
  next();
};

const cb2 = (req, res) => {
  res.send('Hello from C!');
};

app.get('/example/c', [cb0, cb1, cb2]);
```

### レスポンスメソッド

以下のメソッドは、レスポンスオブジェクトに対して使用できる。

- res.download(): ファイルをダウンロードする
- res.end(): レスポンスプロセスを終了する
- res.json(): JSONレスポンスを送信する
- res.jsonp(): JSONPサポートを使用してJSONレスポンスを送信する
- res.redirect(): リダイレクト要求を実行する
- res.render(): ビューをレンダリングする
- res.send(): 各種タイプのレスポンスを送信する
- res.sendFile(): ファイルを送信する
- res.sendStatus(): 状態コードを設定してレスポンスを送信する

## ミドルウェア

expressのミドルウェアとは、リクエストオブジェクト(req)、レスポンスオブジェクト(res)を表すオブジェクトを引数に取る関数である。  
受け取ったリクエストからレスポンスを返すか、次のミドルウェア関数(next)を呼び出すことができる。

以下の例では、第2引数に与えられている関数がミドルウェアである。  
reqにはルートにアクセスされた際の情報が入っている。  
resにはレスポンスを返すための関数が入っており、メソッドチェーンでレスポンスを返すことができる。

```typescript
app.get('/', (req, res) => {
  res.status(200).send('Hello World!');
});
```
