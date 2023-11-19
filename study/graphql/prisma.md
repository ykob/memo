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

## Authentication with Yoga

- [Authentication – GraphQL Yoga](https://the-guild.dev/graphql/yoga-server/tutorial/advanced/01-authentication)

## Migration

マイグレーションとは、データベースに保存されているデータを保持したまま、テーブルの作成やカラムの追加、削除などの変更を行うための機能。

- [Prisma Migrate](https://www.prisma.io/docs/concepts/components/prisma-migrate)

Prismaでは`prisma migrate`コマンドを使ってマイグレーションを行う。  
マイグレーションを実行すると、`schema.prisma`であらかじめ定義されたモデルを元にマイグレーションファイルが生成され、データベースのテーブルやカラムが作成・更新される。

```bash
npx prisma migrate dev --name init
```

## Prisma Schema

### Relations

`@relation`ディレクティブを使って、スキーマ内で定義されている2つのモデルをつなぐ。

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  posts Post[]
}

model Post {
  id       Int  @id @default(autoincrement())
  title    String
  author   User @relation(fields: [authorId], references: [id])
  authorId Int
}
```
