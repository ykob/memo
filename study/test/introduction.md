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
モックデータやテストに利用できるデータを容易に作成・出力することができる。

- [faker-js/faker: Generate massive amounts of fake data in the browser and node.js](https://github.com/faker-js/faker)
