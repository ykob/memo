# GraphQL の開発環境

## GraphQL サーバーの構築

GraphQL サーバーは、GraphQL クエリを受け取り、処理し、要求されたデータを提供する役割を果たすサーバーサイドのコンポーネントのことを指す。

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

- Code-First のスキーマ定義: TypeScript のクラスを使用して GraphQL スキーマを定義する。
- 型安全性と自動補完: Nexus を使用することで、スキーマ定義中に発生する多くのエラーやタイプミスをコンパイル時に検出できる。また、IDE での自動補完により、正確なスキーマの作成が容易になる。
- DSL（Domain-Specific Language）: 独自の DSL の提供により、スキーマの定義をより簡潔かつ直感的に行える。クラスやデコレータの組み合わせにより、複雑なスキーマもシンプルに表現できる。
- リゾルバ関数の自動生成: スキーマ内のフィールドに対するリゾルバ関数を自動生成する。これにより、スキーマとリゾルバの整合性を保ちつつ、コードを簡素化できる。
- エクステンションとモジュール: スキーマの拡張やモジュール化をサポートする。
- リレーションシップのサポート: リレーションシップやインターフェース、ユニオン型などの高度なスキーマ要素もサポートしており、複雑なデータモデリングも可能。

#### チュートリアル

https://nexusjs.org/docs/getting-started/tutorial/

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

## 参考リンク

- [TypeScript の code-first な GraphQL 開発ツール比較: TypeGraphQL vs Nexus vs Pothos - maybe daily dev notes](https://tmokmss.hatenablog.com/entry/20230109/1673237629)
- [Comparison | React Query vs SWR vs Apollo vs RTK Query vs React Router | TanStack Query Docs](https://tanstack.com/query/latest/docs/react/comparison?from=reactQueryV3&original=https%3A%2F%2Ftanstack.com%2Fquery%2Fv3%2Fdocs%2Fcomparison)
