# GraphQL の開発環境

## GraphQL サーバーの構築

### Apollo Client

https://www.apollographql.com/docs/react/

クライアント側での GraphQL データ管理とクエリの実行を容易にするライブラリ。

- データ管理: Apollo Client は、アプリケーションのデータをキャッシュし、クライアント内でデータの共有とキャッシュの管理を行う。
- クエリとミューテーションの実行: Apollo Client は、GraphQL クエリとミューテーションを実行し、サーバーからデータを取得または変更するための機能を提供する。また、クエリの結果をキャッシュに保存して再利用することもできる。
- リアルタイムデータ: Apollo Client は、GraphQL のサブスクリプションを使用してリアルタイムデータの更新をサポートする。
- UI フレームワークとの統合: Apollo Client は、主要な UI フレームワーク（React、Angular、Vue など）との統合を提供し、コンポーネント内でクエリを実行し、データを管理できるようにする。

### Apollo Server

https://www.apollographql.com/docs/apollo-server/

GraphQL サーバーを構築するためのフルスタックなライブラリ。

- GraphQL サーバー構築: Apollo Server は、GraphQL スキーマとリゾルバ関数を使用して、クライアントからの GraphQL リクエストを受け取り、適切なデータを返すサーバーを構築する。
- スキーマ定義とリゾルバ: Apollo Server は、Schema-First なアプローチをサポートしており、スキーマを定義し、それに対するリゾルバ関数を実装することで、API の形状と振る舞いを定義する。
- データソースの統合: Apollo Server は、さまざまなデータソース（データベース、REST API、外部サービスなど）との統合を容易にし、リゾルバ内でデータの取得や更新を処理するための機能を提供する。
- データのキャッシュとキャッシュの管理: Apollo Client と連携してデータキャッシュを効果的に管理するための機能を提供する。

### TypeGraphQL

https://typegraphql.com/

TypeGraphQL は、TypeScript を使用して GraphQL API を定義するためのライブラリ。  
スキーマファーストとコードファーストのアプローチの両方をサポートしている。

- クラスとデコレータ: TypeGraphQL は、クラスとデコレータを使用して、クライアントからのクエリやミューテーションのための GraphQL スキーマを定義する。
- リゾルバ関数の自動生成: TypeGraphQL は、デコレータを使用してリゾルバ関数を自動的に生成し、スキーマとリゾルバの同一性を保つことができる。
- コードファーストとスキーマファーストの両方: TypeGraphQL は、コードファーストとスキーマファーストのハイブリッドなアプローチを提供し、フレキシブルな API 定義を実現する。