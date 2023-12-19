# Express / Prisma を用いたJWT認証機能の実装

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

あとでかく
