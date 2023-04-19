# Cloud Formation

- [AWS CloudFormation の概要 - AWS CloudFormation](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/Welcome.html)

AWS CloudFormation は、AWS リソースの簡単なプロビジョニングと管理を可能にするマネージドサービス。  
CloudFormation を使用すると、インフラの構成をコードで表現することができる。

## CloudFormation の主なメリット

- リソースの自動作成: CloudFormation を使用することで手動でリソースを作成する必要がなくなる。また、必要なリソースを 1 つのテンプレートで定義することで、複数のリソースを一度に作成できる。
- インフラストラクチャの維持管理: 更新作業をテンプレートの編集だけで済ませることができ、手動での変更ミスを防止できる。
- 再利用性の高さ: テンプレートを再利用することで、同じような構成のリソースを複数のアカウントやリージョンに展開することが容易になる。
- 一貫性の保持: リソース間での依存関係を考慮する必要がなくなり、一貫性が保たれる。

## テンプレートセクション

CloudFormation では、テンプレートを以下のセクションに分けて記述する。

| セクション | 必須/任意 | 説明 |
| --- | --- | --- |
| AWSTemplateFormatVersion | 必須 | テンプレートが準拠している AWS CloudFormation テンプレートバージョン |
| Description | 任意 | テンプレートの説明 |
| Metadata | 任意 | テンプレートに関する追加情報を提供するオブジェクト |
| Parameters | 任意 | 実行時（スタックを作成・更新するとき）にテンプレートに渡す値 |
| Rules | 任意 | テンプレートの構文を検証する |
| Mappings | 任意 | キーと関連する値のマッピングで、条件パラメータ値の指定に使用する |
| Conditions | 任意 | リソースの作成や更新を制御する条件 |
| Transform | 任意 | テンプレートの内容を変換する |
| Resources | 必須 | EC2インスタンス、S3バケットなどのスタックリソースとそのプロパティ |
| Outputs | 任意 | スタックのプロパティを確認すると返される値 |
