# GraphQL の開発環境

## GraphQL サーバーの構築

GraphQL サーバーは、GraphQL クエリを受け取り、処理し、要求されたデータを提供する役割を果たすサーバーサイドのコンポーネントのことを指す。

### Apollo Server

https://www.apollographql.com/docs/apollo-server/

GraphQL サーバーを構築するためのフルスタックなライブラリ。

- 単独で GraphQL サーバを構築することができる
- Schema-First なアプローチ
- スキーマを定義し、それに対するリゾルバ関数を実装する
- express と合わせて使用することで、ミドルウェアの導入などのカスタマイズを容易に行うことができる
- スキーマ、リゾルバの生成を容易にする他のライブラリ（TypeGraphQL, Nexus 等）と組み合わせることができる

#### Context

Apollo Server の Context オブジェクトは、リゾルバ間でデータや状態を共有するために使用される。  
リゾルバは GraphQL スキーマ上の特定のフィールドに対する値を解決するための関数だが、Context オブジェクトは、このリゾルバ関数に渡されるパラメータの一部となる。  
Context に含める情報の一例として以下が挙げられる。

- 認証と認可の情報
- Rest API のエンドポイント
- 共通のカスタムロジックやヘルパーメソッド
- リクエスト固有の情報（言語設定、クライアントのデバイス情報、等）

### TypeGraphQL

https://typegraphql.com/

TypeGraphQL は、TypeScript を使用して GraphQL API を定義するためのライブラリ。  
Schema-First と Code-First のアプローチの両方をサポートしている。

- クラスとデコレータ: TypeGraphQL は、クラスとデコレータを使用して、クライアントからのクエリやミューテーションのための GraphQL スキーマを定義する。
- リゾルバ関数の自動生成: TypeGraphQL は、デコレータを使用してリゾルバ関数を自動的に生成し、スキーマとリゾルバの同一性を保つことができる。
- Code-First と Schema-First の両方: TypeGraphQL は、Code-First と Schema-First のハイブリッドなアプローチを提供し、フレキシブルな API 定義を実現する。

2023/8/28 時点での最新版は v1.1.1（2020/11/5 リリース）となっている。  
また、v1.2.0-rc.1 が 2021/10/6、2.0.0-beta.3 が 2023/8/18 にそれぞれプレリリースされている。

### GraphQL Nexus

https://nexusjs.org/

GraphQL スキーマを定義するための TypeScript ベースのライブラリ。  
スキーマの Code-First 定義を強化し、柔軟性と型安全性を提供する。

- Code-First なアプローチ
- TypeScript のクラスを使用して GraphQL スキーマを定義する。
- スキーマ定義中に発生する多くのエラーやタイプミスをコンパイル時に検出できる。
- 独自の DSL(Domain-Specific Language) の提供により、スキーマの定義をより簡潔かつ直感的に行える。
- IDE での自動補完により、正確なスキーマの作成が容易になる。
- スキーマ内のフィールドに対するリゾルバ関数を自動生成する。これにより、スキーマとリゾルバの整合性を保ちつつ、コードを簡素化できる。

#### チュートリアル

https://nexusjs.org/docs/getting-started/tutorial/

- このチュートリアルに記載されているコードは 2023/8/29 時点ではそのままでは動かない。最新版のモジュールに対応した書き方に変更する必要がある。

### Pothos GraphQL

https://pothos-graphql.dev/

## GraphQL クライアントの構築

GraphQL クライアントは、GraphQL サーバーと通信するためのライブラリやツールのことを指す。  
ウェブアプリケーションのコード内に埋め込まれ、ユーザーがアプリケーションを使用する際に GraphQL サーバーと通信する役割を果たす。  
ユーザーのブラウザ内で実行され、ネットワークリクエストを生成し、GraphQL サーバーに対してクエリを送信する。  
また、クエリの結果を受信し、表示するためのデータをウェブアプリケーションに提供する。

### Apollo Client

https://www.apollographql.com/docs/react/

クライアント側での GraphQL データ管理とクエリの実行を容易にするライブラリ。

- データ管理: Apollo Client は、アプリケーションのデータをキャッシュし、クライアント内でデータの共有とキャッシュの管理を行う。
- クエリとミューテーションの実行: Apollo Client は、GraphQL クエリとミューテーションを実行し、サーバーからデータを取得または変更するための機能を提供する。また、クエリの結果をキャッシュに保存して再利用することもできる。
- リアルタイムデータ: Apollo Client は、GraphQL のサブスクリプションを使用してリアルタイムデータの更新をサポートする。
- UI フレームワークとの統合: Apollo Client は、主要な UI フレームワーク（React、Angular、Vue など）との統合を提供し、コンポーネント内でクエリを実行し、データを管理できるようにする。

### SWR

https://swr.vercel.app/

React アプリケーション向けに設計されたデータ取得とキャッシュのライブラリ。  
REST API、GraphQL、ローカルキャッシュなど、様々なデータソースと統合することができる。

- データの取得とキャッシュ: SWR は、データの取得とキャッシュを同時に管理する。初回のデータ取得時にキャッシュにデータを格納し、それをクライアント側で利用可能な状態として保持する。
- Stale-While-Revalidate: SWR は、データのキャッシュが「古い」（stale）場合でも、その古いデータをクライアントに即座に返すことから、「古くなっている間」（while）に、バックグラウンドで新しいデータを取得し、キャッシュを最新にするプロセス（revalidate）をサポートする。
- オプティミスティックな UI レスポンス: SWR は、リクエストを非同期に行うため、ユーザーの操作に対する迅速な UI レスポンスを提供する。
- リトライとエラーハンドリング: ネットワークの問題や API エラーに対応するため、SWR は自動的にリトライやエラーハンドリングを行う。
- インターバルリフェッチ: SWR は、指定したインターバルでデータの再取得をサポートする。

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
