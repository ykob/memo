# テスト

## テストの種類

| 種類 | 説明 | ツール |
| --- | --- | --- |
| 静的テスト | プログラムを実行せずソースコードのチェックなどによって行う。 | ESLint<br>TypeScript |
| 単体テスト | 個々の独立したモジュールや関数が期待どおりに動作するかどうかを検証する。<br>ユニットテストと呼ばれることもある。 | Jest |
| 結合テスト | 複数のモジュールや関数が組み合わさった状態で、<br>期待通りの動作が得られるかどうかを検証する。 | Jest |
| E2Eテスト | エンドユーザーの視点に立ってUIを操作することで、<br>アプリケーションが期待どおりに動作するかを検証する。<br>UIテストと呼ばれることもある。 | cypress<br>CodeceptJS |

## よく使うライブラリ

### Faker (faker.js)

ダミーデータを生成するためのライブラリ。
モックやテストに利用できるデータを容易に作成・出力することができる。

- [faker-js/faker: Generate massive amounts of fake data in the browser and node.js](https://github.com/faker-js/faker)

### Fishery

テストに使用するための JavaScript オブジェクトを生成するためのライブラリ。  
`Factory` という関数を用いてオブジェクトを生成する。  
`Factory.define()` で独自のオブジェクトを生成するための Factory 関数を作成し、 `factory.build()` を実行して実際にオブジェクトを生成する。  
`buildList(count)` を使用することで同様の型のオブジェクトを複数同時に生成することもできる。

- [thoughtbot/fishery: A library for setting up JavaScript objects as test data](https://github.com/thoughtbot/fishery)

### msw (Mock Server Worker)

API リクエストのモック化を行うライブラリ。  
テスト時に実行される API リクエストを実際のアプリケーションサーバに対して行うのではなく、 msw によって作成されたサーバーに対して行われるようにモック化をして、テストの安定化を図る。  
REST API だけでなく GraphQL にも対応している。

- [MSW – Seamless API mocking library for browser and Node | Mock Service Worker](https://mswjs.io/)