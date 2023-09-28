# テスト

## テストを書くメリット

- バグの早期発見に繋がる
- リファクタ時に影響範囲を把握しやすくする
- コードレビューの負担を減らす
- 長期的な開発コストの軽減につながる

## 用語

| 用語 | 説明 |
| --- | --- |
| アサーション | テストコード上の、検証値が期待どおりであるという記述 |
| マッチャー | アサーションを構成する、期待値の条件を指定する記述 |

## テストの種類

| 種類 | 説明 | ツール |
| --- | --- | --- |
| 静的テスト | プログラムを実行せずソースコードのチェックなどによって行う。 | ESLint<br>TypeScript |
| 単体テスト | 個々の独立したモジュールや関数が期待どおりに動作するかどうかを検証する。<br>ユニットテストと呼ばれることもある。 | Jest<br>Testing Library |
| 結合テスト | 複数のモジュールや関数が組み合わさった状態で、<br>期待通りの動作が得られるかどうかを検証する。 | Jest<br>Testing Library |
| E2E テスト | エンドユーザーの視点に立って UI を操作することで、<br>アプリケーションが期待どおりに動作するかを検証する。<br>UI テストと呼ばれることもある。 | cypress<br>CodeceptJS   |
| ビジュアルリグレッションテスト | ある時点の前後の画面描画を比較して、レイアウト崩れを検出する。 | Storybook<br>reg-suit |

## よく使うライブラリ

### Faker (faker.js)

ダミーデータを生成するためのライブラリ。
モックやテストに利用できるデータを容易に作成・出力することができる。

- [faker-js/faker: Generate massive amounts of fake data in the browser and node.js](https://github.com/faker-js/faker)

### Fishery

テストに使用するためのJavaScriptオブジェクトを生成するためのライブラリ。  
`Factory` という関数を用いてオブジェクトを生成する。  
`Factory.define()` で独自のオブジェクトを生成するための Factory 関数を作成し、 `factory.build()` を実行して実際にオブジェクトを生成する。  
`buildList(count)` を使用することで同様の型のオブジェクトを複数同時に生成することもできる。

- [thoughtbot/fishery: A library for setting up JavaScript objects as test data](https://github.com/thoughtbot/fishery)

### msw (Mock Server Worker)

APIリクエストのモック化を行うライブラリ。  
テスト時に実行されるAPIリクエストを実際のアプリケーションサーバに対して行うのではなく、mswによって作成されたサーバーに対して行われるようにモック化をして、テストの安定化を図る。  
REST APIだけでなくGraphQLにも対応している。

- [MSW – Seamless API mocking library for browser and Node | Mock Service Worker](https://mswjs.io/)
