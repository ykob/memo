# GraphQL Schema-First と Code-First

Schema-First と Code-First、いずれも GraphQL 開発の手法の名称である。

## Schema-First

スキーマを書いてからコードを生成する。

- スキーマをフロントエンド/バックエンド共通の所有物にできる
- スキーマ(SDL)を手書きするのはつらい

## Code-First

コードから直接 GraphQL サーバーを作る。  
開発者はリゾルバを書くだけでよく、スキーマ(SDL)は自動生成される。

- コードが常にスキーマと一致する
- モジュール単位でファイルを分割できる
- スキーマがバックエンドの所有物になってしまう

## 参考リンク

- [Schema-First vs Code-Only GraphQL - Apollo GraphQL Blog](https://www.apollographql.com/blog/backend/architecture/schema-first-vs-code-only-graphql/)
- [コードファースト vs スキーマファースト on Rust / code-first-vs-schema-first - Speaker Deck](https://speakerdeck.com/haco/code-first-vs-schema-first?slide=10)
