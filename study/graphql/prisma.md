# GraphQLサーバーのORMにPrismaを使う

- [Prisma](https://www.prisma.io/)

## Prismaとは

Node.jsやTypeScript、Rustなどの言語で書かれたアプリケーションから、データベースへのアクセスを行うためのORM（Object Relational Mapping）。
以下3つのモジュールから構成されている。

- Prisma Client
- Prisma Migrate
- Prisma Studio

## ORMとは

データベースのテーブルとオブジェクト指向プログラミング言語のオブジェクトをマッピングするための仕組み。  
SQLを直接書くことなく、オブジェクト指向プログラミング言語でデータベースを操作できる。

## Migration

マイグレーションとは、データベースに保存されているデータを保持したまま、テーブルの作成やカラムの追加、削除などの変更を行うための機能。

- [Prisma Migrate](https://www.prisma.io/docs/concepts/components/prisma-migrate)

Prismaでは`prisma migrate`コマンドを使ってマイグレーションを行う。  
マイグレーションを実行すると、`schema.prisma`であらかじめ定義されたモデルを元にマイグレーションファイルが生成され、データベースのテーブルやカラムが作成・更新される。

```bash
npx prisma migrate dev --name init
```

## Prisma Schema

Prismaのセットアップに用いられる構成ファイル。  
`schema.prisma`というファイル名で用いられることが多い。  
`generator`、`datasource`、`model` の3つのブロックから構成される。

### Generator

データモデルをもとに生成されるクライアントを定義するためのブロック。  
`generator`ディレクティブを使う。  
代表的なクライアントとしては、Prisma Clientがある。

```prisma
generator client {
  provider = "prisma-client-js"
}
```

### Datasource

データベースの接続情報を定義するためのブロック。  
`datasource`ディレクティブを使う。  
`provider`プロパティにデータベースの種類を指定し、`url`プロパティにデータベースの接続情報を記述する。

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

### Model

アプリケーションのデータモデルとリレーションを定義するためのブロック。

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  name  String?
  posts Post[]
}
```

#### Relations

`@relation`ディレクティブを使って、スキーマ内で定義されている2つのモデルをつなぐことができる。

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  name  String?
  posts Post[]
}

model Post {
  id       Int  @id @default(autoincrement())
  title    String
  author   User @relation(fields: [authorId], references: [id])
  authorId Int
}
```

## References

- [Authentication – GraphQL Yoga](https://the-guild.dev/graphql/yoga-server/tutorial/advanced/01-authentication)