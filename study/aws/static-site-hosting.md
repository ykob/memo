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
3. CloudFront Functions を作成する
4. CloudFront に作成した CloudFront Functions を紐付ける

### 1. S3 に静的サイトをホスティングするためのバケットを作成する

S3 のコンソールからバケットを作成する。

- バケット名: 任意の名前
- リージョン: 任意のリージョン
- ブロックパブリックアクセス: ブロックしない
- バージョニング: 有効にする
- ログ記録: 有効にする
- デフォルトの暗号化: 有効にする
- バケットのアクセス制御: プライベート
- バケットポリシー: 任意のポリシーを設定する

#### バケットポリシー

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::バケット名/*"
    }
  ]
}
```
