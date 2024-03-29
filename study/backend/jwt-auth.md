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

Expressでは`express.Router()`を用いてルーティングを定義することができる。  
これによってAPIのエンドポイントをグループ化することができる。

```typescript
const router = express.Router();

router.get("/", (req, res) => {
  res.send("Hello World!");
});
```

ユーザー情報に関するAPIルートは以下のように定義する。

- GET `/users` : ユーザー情報のリストを検索する
- GET `/users/:id` : ユーザーIdをキーにして既存のユーザー情報を検索する
- POST `/users` : Emailとパスワードを入力してユーザー情報を新規作成する
- PUT `/users/:id` : ユーザー情報を更新する
- DELETE `/users/:id` : ユーザー情報を削除する

### 認証処理 `Auth` に関連するサービスを定義する

- `addRefreshTokenToWhitelist` : トークンID、ハッシュ値、ユーザーIDを元に、リフレッシュトークンを新規発行する
- `findRefreshToken` : トークンIdをキーにして既存のリフレッシュトークンを検索する
- `deleteRefreshToken` : トークンIdをキーにして既存のリフレッシュトークンを無効化する
- `revokeTokens` : ユーザーIDをキーにして、該当するリフレッシュトークンを無効化する

ログアウトの処理はクライアントサイドでアクセストークンを保存したCookieまたはLocalStorageを消去すればよいので、サービス側では考慮をしない。

### 認証処理 `Auth` に関連するAPIルートを定義する

認証処理に関するAPIルートは以下のように定義する。

- POST `/auth/register` : Emailとパスワードを入力してユーザー情報を新規作成する
- POST `/auth/login` : Emailとパスワードを入力してログインする
- POST `/auth/refreshtoken` : リフレッシュトークンを入力してその有効性を確認し、問題なければリフレッシュトークンとアクセストークンを新規に生成する
- POST `/auth/revoke-refresh-token` : リフレッシュトークンを無効化する

認証処理のルートではPrismaClientによるデータの操作以外にも、ユーザーやトークンの有効性も合わせて検証する。

#### POST `/auth/register`

ユーザー情報の新規作成では以下の手順に沿って処理を行う。

1. 入力されたEmailとパスワードのバリデーションを行う。入力値が不正な場合は400エラーを返す。
2. 入力されたEmailが既に登録されていないかを確認する。既に登録されている場合は400エラーを返す。
3. ユーザー情報を新規作成する。
4. UUIDを用いてアクセストークンとリフレッシュトークンを発行する。
5. リフレッシュトークンをデータベースに保存する。
6. 発行したアクセストークンとリフレッシュトークンをAPIルート呼び出し元に返す。

#### POST `/auth/login`

ログイン処理では以下の手順に沿って処理を行う。

1. 入力されたEmailとパスワードのバリデーションを行う。入力値が不正な場合は400エラーを返す。
2. 入力されたEmailが登録されているかを確認する。登録されていない場合は403エラーを返す。
3. 入力されたパスワードが登録されているパスワードと一致するかを確認する。一致しない場合は403エラーを返す。
4. UUIDを用いてアクセストークンとリフレッシュトークンを発行する。
5. リフレッシュトークンをデータベースに保存する。
6. 発行したアクセストークンとリフレッシュトークンをAPIルート呼び出し元に返す。

#### POST `/auth/refreshtoken`

リフレッシュトークンの生成と保存は以下の手順に沿って処理を行う。

1. APIルート呼び出し元からリフレッシュトークンを受け取る。
2. リフレッシュトークンのバリデーションを行う。入力値が不正な場合は400エラーを返す。
3. リフレッシュトークンがデータベースに登録されているかを確認する。登録されていない場合は401エラーを返す。
4. リフレッシュトークンが有効かどうかを確認する。無効化されている場合は401エラーを返す。
5. リフレッシュトークンのハッシュ値が登録されているハッシュ値と一致するかを確認する。一致しない場合は401エラーを返す。
6. リフレッシュトークンのユーザーIDが登録されているユーザーIDと一致するかを確認する。一致しない場合は401エラーを返す。
7. 古いリフレッシュトークンを無効化する。
8. UUIDを用いてアクセストークンとリフレッシュトークンを新規発行する。
9. リフレッシュトークンをデータベースに保存する。
10. 発行したアクセストークンとリフレッシュトークンをAPIルート呼び出し元に返す。

#### POST `/auth/revoke-refresh-token`

リフレッシュトークンの無効化は以下の手順に沿って処理を行う。

1. APIルート呼び出し元からユーザーIDを受け取る。
2. ユーザーIDと一致する有効なリフレッシュトークン全件をデータベースから検索する。
3. 検索結果のリフレッシュトークンを無効化する。

### 認証処理のミドルウェアを作成する

Expressのミドルウェアは、`req`、`res`、`next`の3つの引数を受け取る関数であり、  
リクエストレスポンスサイクルの途中で共通の処理を実行したい場合に用いることができる。

```typescript
const middleware = (req, res, next) => {
  // do something
  next();
};
```

今回の認証機能では、アクセストークンの有効性を検証するためのミドルウェア`isAuthenticated`を作成する。  
`isAuthenticated`では主に以下の処理を行う。

1. リクエストヘッダーからアクセストークン`authorization`を取得する。
2. アクセストークンが存在しない場合は401エラーを返す。
3. `jsonwebtoken.verify()`を用いてアクセストークンの有効性を検証する。有効でない場合は401エラーを返す。
4. 有効な場合は`next()`を実行して、プロセスの次の処理に進む。

ソースコードでは以下のように記述する。

```typescript
import { NextFunction, Request, Response } from "express";
import { TokenExpiredError, verify } from "jsonwebtoken";
import { env } from "./env";

export const isAuthenticated = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const { authorization } = req.headers;

  if (!authorization) {
    res.status(401);
    throw new Error("🚫 Un-Authorized 🚫");
  }

  try {
    const token = authorization.split(" ")[1];

    verify(token, env.JWT_ACCESS_SECRET);
  } catch (err) {
    res.status(401);
    if (err instanceof TokenExpiredError) {
      throw new Error(err.name);
    }
    throw new Error("🚫 Un-Authorized 🚫");
  }

  return next();
};
```

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