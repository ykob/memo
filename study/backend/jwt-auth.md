# Express / Prisma を用いたJWT認証機能の実装

## 検証用リポジトリ

https://github.com/ykob/learn-nodejs-with-express

## JWTとは

JWTとは、JSON Web Tokenの略で、JSON形式で情報をやり取りするための仕様のこと。  
JWTと書いてジョットと読む。  
JWTはRFC 7519で定義されている。

JWTは、以下の3つの文字列をピリオドで区切った文字列で表される。

- ヘッダー
- ペイロード
- 署名

## 使用する環境

| Module | Usage |
| --- | --- |
| [Express](https://expressjs.com/ja/) | Node.jsのウェブアプリケーションフレームワーク。アプリケーションサーバの機能全般の構築に利用する。 |
| [Prisma](https://www.prisma.io/) | Node.js/TypeScript用のORMライブラリ。データベースの構築と接続に利用する。 |
| [bcryptjs](https://www.npmjs.com/package/bcryptjs) | パスワードの文字列をハッシュ化したり、ハッシュ値と文字列を比較したりするライブラリ。 |
| [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) | JWTを発行したり、JWTの値をバリデーションにかけることができるライブラリ。 |
| [uuid](https://www.npmjs.com/package/uuid) | UUID(Universally Unique IDentifier)を発行するライブラリ。ここではjti(JWT ID)に利用する。 |

## 参考リンク

- [RFC 7519 - JSON Web Token (JWT)](https://datatracker.ietf.org/doc/html/rfc7519)
- [JWT Authentication using Prisma and Express - DEV Community](https://dev.to/mihaiandrei97/jwt-authentication-using-prisma-and-express-37nk)

## 手順

- schema.prismaに、ユーザー情報 `User` とリフレッシュトークン `RefreshToken` のモデルを追加する。
- ユーザー情報 `User` に関連するサービスを定義する
- ユーザー情報 `User` に関連するAPIルートを定義する
- 認証処理 `Auth` に関連するサービスを定義する
- 認証処理 `Auth` に関連するAPIルートを定義する
- 認証処理のミドルウェアを作成する

### `schema.prisma`に、ユーザー情報 `User` とリフレッシュトークン `RefreshToken` のモデルを追加する。

以下のようにモデルを定義する。

```prisma
model User {
  id           String         @id @unique @default(uuid())
  email        String         @unique
  password     String
  refreshToken RefreshToken[]
  createdAt    DateTime       @default(now())
  updatedAt    DateTime       @updatedAt
}

model RefreshToken {
  id          String   @id @unique @default(uuid())
  hashedToken String
  userId      String
  user        User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  revoked     Boolean  @default(false)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}
```

#### `User`

- `email`と`password`は登録とログインに用いる。
- `refreshToken`を追加し、そのユーザーに対して発行されたリフレッシュトークンを紐づける。

#### `RefreshToken`

- `hashedToken`にリフレッシュトークンのハッシュ値を保存する。 
- `userId`と`user`を追加し、リフレッシュトークンを発行した対象ユーザーを紐づける。
- `revoked`によってトークンが無効化されたかどうかを判断する。

#### 補足

- アクセストークンは発行直後にCookieやLocalStorageに保存される想定で、データベースには保存しない。

### ユーザー情報 `User` に関連するサービスを定義する

- `findUsers` : ユーザー情報のリストを検索する
- `findUserById` : ユーザーIdをキーにして既存のユーザー情報を検索する
- `findUserByEmail` : Emailをキーにして既存のユーザー情報を検索する
- `createUserByEmailAndPassword` : Emailとパスワードを入力してユーザー情報を新規作成する
- `updateUser` : ユーザー情報を更新する
- `deleteUser` : ユーザー情報を削除する

### ユーザー情報 `User` に関連するAPIルートを定義する

### 認証処理 `Auth` に関連するサービスを定義する

- `addRefreshTokenToWhitelist` : トークンID、ハッシュ値、ユーザーIDを元に、リフレッシュトークンを新規発行する
- `findRefreshToken` : トークンIdをキーにして既存のリフレッシュトークンを検索する
- `deleteRefreshToken` : トークンIdをキーにして既存のリフレッシュトークンを無効化する
- `revokeTokens` : ユーザーIDをキーにして、該当するリフレッシュトークンを無効化する

ログアウトの処理はクライアントサイドでアクセストークンを保存したCookieまたはLocalStorageを消去すればよいので、サービス側では考慮をしない。

### 認証処理 `Auth` に関連するAPIルートを定義する

### 認証処理のミドルウェアを作成する

## 補足

- 環境変数の型を定義する

### 環境変数の型を定義する

本件では少なくとも以下3件の環境変数を定義している。

- `DATABASE_URL`
- `JWT_ACCESS_SECRET`
- `JWT_REFRESH_SECRET`

これらはすべて`string`型として扱うことになるが、そのまま読み込むと型が定義されていない状態になる。  
これらに型を定義するために以下モジュールを用いる。

| Module | Usage |
| --- | --- |
| [@t3-oss/env-core](https://github.com/t3-oss/t3-env) | `createEnv`関数で型付けされた環境変数を返す。 |
| [dotenv](https://github.com/motdotla/dotenv) | `config`関数で`.env`ファイルを読み込む。 |
| [zod](https://github.com/colinhacks/zod) | スキーマ宣言・データ検証ライブラリ。ここでは環境変数のスキーマ宣言に用いる。 |


具体的には以下のようにTypeScriptのコードを記述し、環境変数を参照する呼び出し元でimportして使用する。

```typescript
import { createEnv } from "@t3-oss/env-core";
import { config } from "dotenv";
import { z } from "zod";

config();

export const env = createEnv({
  server: {
    DATABASE_URL: z.string().url(),
    JWT_ACCESS_SECRET: z.string(),
    JWT_REFRESH_SECRET: z.string(),
  },
  runtimeEnv: process.env,
});
```