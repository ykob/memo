# GraphQL の開発環境

## GraphQL サーバーの構築

GraphQL サーバーは、GraphQL クエリを受け取り、処理し、要求されたデータを提供する役割を果たすサーバーサイドのコンポーネントのことを指す。

### GraphQL サーバー構築用ライブラリの候補

- [Apollo Server](https://www.apollographql.com/docs/apollo-server/)
- [GraphQL Yoga](https://the-guild.dev/graphql/yoga-server)

### GraphQL スキーマ/リゾルバ定義用ライブラリの候補

- [TypeGraphQL](https://typegraphql.com/)
- [GraphQL Nexus](https://nexusjs.org/)
- [Pothos GraphQL](https://pothos-graphql.dev/)

### Apollo Server

#### Context

Apollo Server の Context オブジェクトは、リゾルバ間でデータや状態を共有するために使用される。  
リゾルバは GraphQL スキーマ上の特定のフィールドに対する値を解決するための関数だが、Context オブジェクトは、このリゾルバ関数に渡されるパラメータの一部となる。  
Context に含める情報の一例として以下が挙げられる。

## GraphQL クライアントの構築

GraphQL クライアントは、GraphQL サーバーと通信するためのライブラリやツールのことを指す。  
ウェブアプリケーションのコード内に埋め込まれ、ユーザーがアプリケーションを使用する際に GraphQL サーバーと通信する役割を果たす。  
ユーザーのブラウザ内で実行され、ネットワークリクエストを生成し、GraphQL サーバーに対してクエリを送信する。  
また、クエリの結果を受信し、表示するためのデータをウェブアプリケーションに提供する。

### Apollo Client

https://www.apollographql.com/docs/react/

クライアント側での GraphQL データ管理とクエリの実行を容易にするライブラリ。

#### `@apollo/datasource-rest`

https://github.com/apollographql/datasource-rest

REST API を用いるデータのフェッチ、キャッシング、データの重複排除といったロジックをカプセル化した `RESTDataSource` というクラスを、Apollo Server の環境下に提供する。

### SWR

https://swr.vercel.app/

React アプリケーション向けに設計されたデータ取得とキャッシュのライブラリ。  
REST API、GraphQL、ローカルキャッシュなど、様々なデータソースと統合することができる。

### GraphQL Code Generator

- [Home – GraphQL Code Generator](https://the-guild.dev/graphql/codegen)

GraphQL スキーマを基に、クライアントアプリケーション用の型定義やクエリクライアントを生成する。  
また、出力する言語やフレームワークに応じたプラグインが多数用意されている。

- `@graphql-codegen/cli`
  - コアコマンドラインインターフェース（CLI）で、GraphQL Code Generator をコマンドラインから簡単に実行できるようにするためのツール。
- `@graphql-codegen/typescript`
  - TypeScript の型定義を生成するための基本的なプラグイン。
  - 他の TypeScript プラグインが使用する基礎的な型定義を生成するので、導入は必須。
- `@graphql-codegen/typescript-operations`
  - GraphQL スキーマからクエリやミューテーション、またその引数や戻り値の型定義を生成する。
- `@graphql-codegen/typescript-graphql-request`
  - [graphql-request](https://github.com/jasonkuhrt/graphql-request)というライブラリをベースにしたクライントを生成する。
- `@graphql-codegen/typescript-resolvers`
  - GraphQL リゾルバー関数の型定義を生成する。サーバーサイドでの GraphQL スキーマの実装に役立つ。
- `@graphql-codegen/typescript-react-apollo`
  - 別途作成したクエリとミューテーションの GraphQL コードをもとに、 Apollo Client の useQuery を用いたデータ取得用の関数を生成する。
  - `@graphql-codegen/typescript-operations` によって生成されるクエリとミューテーションを用いているため、連携は必須。



## 参考リンク

- [TypeScript の code-first な GraphQL 開発ツール比較: TypeGraphQL vs Nexus vs Pothos - maybe daily dev notes](https://tmokmss.hatenablog.com/entry/20230109/1673237629)
- [Comparison | React Query vs SWR vs Apollo vs RTK Query vs React Router | TanStack Query Docs](https://tanstack.com/query/latest/docs/react/comparison?from=reactQueryV3&original=https%3A%2F%2Ftanstack.com%2Fquery%2Fv3%2Fdocs%2Fcomparison)
- [GraphQL Code Generator(graphql-codegen) おすすめ設定 for TypeScript](https://zenn.dev/izumin/articles/ffc84c1b4310be#typescript-plugin%2C-typescript-operation-plugin)
- [graphql-codegen Client Preset 時代(v3~)の おすすめ設定 for TypeScript](https://zenn.dev/layerx/articles/028cb518cffd61)