# AWS で静的サイトをホスティングする

## 要件

- vite で build した静的サイトのファイル一式をデプロイする
- Basic 認証を使ってアクセスを制限する

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
3. Basic 認証を設定するための CloudFront Functions を作成する
4. CloudFront に作成した CloudFront Functions を紐付ける

### 1. S3 に静的サイトをホスティングするためのバケットを作成する

S3 のコンソールからバケットを作成する。

- バケット名: 任意の名前
- リージョン: `ap-northeast-1`
- オブジェクト所有者: ACL 無効
- ブロックパブリックアクセス: パブリックアクセスをすべてブロック
- バージョニング: 無効にする
- デフォルトの暗号化: 任意

### 2. CloudFront に配信するためのディストリビューションを作成する

CloudFront のコンソールからディストリビューションを作成する。

- オリジンドメイン: 1 で作成した S3 のエンドポイントを指定する。
- オリジンアクセス: Legacy access identities を選択する。
  - オリジンアクセスアイデンティティ > 新しい OAI を作成 を実行する。
  - バケットポリシー: いいえ
- 「デフォルトルートオブジェクト - オプション」に `index.html` を指定する。

### 3. Basic 認証を設定するための CloudFront Functions を作成する

#### Basic 認証に使う ID と Password を定義する。

CloudShell で以下のコマンドを実行したら、出力された文字列をコピーする。

```sh
echo -n 'ID:Password' | base64
```

#### CloudFront Functions を作成する

CloudFront の左メニューから「関数」を選択し、関数を作成する。

- 関数名: 任意の名前
- ランタイム: Node.js 18.x
- ソースコード: 以下のコードをコピペする
  - `authString` には先ほどコピーした文字列を貼り付ける

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
      headers: { "www-authenticate": { value: "Basic" } },
    };
  }

  return request;
}
```

- 発行タブ > 発行ボタンを押下して関数を発行する。

### 4. CloudFront に作成した CloudFront Functions を紐付ける

1. ディストリビューションを選択する。
2. ビヘイビアタブ > 関数を紐付けたいビヘイビアを選択 > 編集
3. 「関数の関連付け」の「ビューワーリクエスト」に作成した関数を追加する。

## Cloud Formation

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: "Statically host a website with S3 and CloudFront."

Parameters:
  DomainName:
    Type: String
    Description: "The domain name of the website."
  BasicAuth:
    Type: String
    Description: "The basic auth string."

Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Ref DomainName
      AccessControl: Private
      VersioningConfiguration:
        Status: Suspended
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: index.html
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: true
        Comment: !Ref DomainName
        DefaultRootObject: index.html
        Origins:
          - DomainName: !GetAtt S3Bucket.DomainName
            Id: !Ref DomainName
            S3OriginConfig:
              OriginAccessIdentity: ""
        DefaultCacheBehavior:
          AllowedMethods:
            - GET
            - HEAD
            - OPTIONS
          CachedMethods:
            - GET
            - HEAD
            - OPTIONS
          Compress: true
          DefaultTTL: 86400
          ForwardedValues:
            Cookies:
              Forward: none
            QueryString: false
          MaxTTL: 31536000
          MinTTL: 0
          TargetOriginId: !Ref DomainName
          ViewerProtocolPolicy: redirect-to-https
        ViewerCertificate:
          AcmCertificateArn: !Ref CertificateArn
          SslSupportMethod: sni-only
        HttpVersion: http2
        PriceClass: PriceClass_100
        Aliases:
          - !Ref DomainName
        Restrictions:
          GeoRestriction:
            RestrictionType: none
        WebACLId: !Ref WebACLId
  CloudFrontFunction:
    Type: AWS::CloudFront::Function
    Properties:
      AutoPublish: true
      FunctionCode:
        Comment: !Ref DomainName
        Handler: index.handler
        Runtime: cloudfront-js-1.0
        Code:
          ZipFile: |
            function handler(event) {
              var request = event.request;
              var headers = request.headers;
              var authString = !Ref BasicAuth;

              if (
                typeof headers.authorization === "undefined" ||
                headers.authorization.value !== authString
              ) {
                return {
                  statusCode: 401,
                  statusDescription: "Unauthorized",
                  headers: { "www-authenticate": { value: "Basic" } },
                };
              }

              return request;
            }
      FunctionConfig:
        Comment: !Ref DomainName
        Runtime: cloudfront-js-1.0
        Name: !Ref DomainName
Outputs:
  S3BucketName:
    Description: "The name of the S3 bucket."
    Value: !Ref S3Bucket
  CloudFrontDistributionId:
    Description: "The ID of the CloudFront distribution."
    Value: !Ref CloudFrontDistribution
  CloudFrontDistributionDomainName:
    Description: "The domain name of the CloudFront distribution."
    Value: !GetAtt CloudFrontDistribution.DomainName
```
