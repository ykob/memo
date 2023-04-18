# AWS で静的サイトをホスティングする

## 要件

- vite で build した静的サイトのファイル一式をデプロイする
- Basic認証を使ってアクセスを制限する

## 使用するサービス

- S3
- CloudFront

### S3

静的サイトをホスティングするためのストレージサービス。  
今回は vite で build したファイル一式をアップロードする。

### CloudFront

S3 にアップロードしたファイルを配信するための CDN サービス。  
今回は CloudFront Functions を用いて Basic 認証を実装する。

## 手順

1. S3 に静的サイトをホスティングするためのバケットを作成する
2. CloudFront に配信するためのディストリビューションを作成する
3. Basic認証を設定するための CloudFront Functions を作成する
4. CloudFront に作成した CloudFront Functions を紐付ける

### 1. S3 に静的サイトをホスティングするためのバケットを作成する

S3 のコンソールからバケットを作成する。

- バケット名: 任意の名前
- リージョン: `ap-northeast-1`
- オブジェクト所有者: ACL無効
- ブロックパブリックアクセス: パブリックアクセスをすべてブロック
- バージョニング: 有効にする
- デフォルトの暗号化: 任意

### 2. CloudFront に配信するためのディストリビューションを作成する

CloudFront のコンソールからディストリビューションを作成する。

### 3. Basic認証を設定するための CloudFront Functions を作成する

CloudFront Functions のコンソールから関数を作成する。

- 関数名: 任意の名前
- ランタイム: Node.js 18.x
- ソースコード: 以下のコードをコピペする

```js
function handler(event) {
  var request = event.request;
  var headers = request.headers;
  var authString = "Basic ABCDEFGHIJKLMNOPQRSTUVWXYZ";

  if (
    typeof headers.authorization === "undefined" ||
    headers.authorization.value !== authString
  ) {
    return {
      statusCode: 401,
      statusDescription: "Unauthorized",
      headers: { "www-authenticate": { value: "Basic" } }
    };
  }
  
  return request;
}
```
