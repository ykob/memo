# 図解まるわかり AWSのしくみ

https://www.amazon.co.jp/dp/B09ZTH1FJW/

## 第1章 AWSを使う

- クラウドコンピューティングのメガベンダー
  - Amazon (AWS)
  - Microsoft (Azure)
  - Google (Google Cloud Platform)
- AWSのユーザーが必要なITリソースを選択して使用する
- AWSのクラウド製品概要
  - コンピューティング（サーバー）... EC2, ECS
  - ストレージ ... S3, EBS
  - データベース ... RDS, DynamoDB
  - ネットワーク ... VPC
  - エンドユーザーコンピューティング(クライアント) ... WorkSpaces, WorkLink
- 複雑な最新技術を比較的容易に利用できる
- Cloud Watch ... AWSで利用するITリソースの運用監視を行うサービス

## 第2章 クラウド利用の基本 -事前に検討すべきこと-

- オンプレミス ... 自社でITリソースを保有して管理する。
- オンプレミスでの場合と同等のシステム構成をクラウド上でどう実現するかを事前にドキュメント化する。
  - 一般的には構成設計、論理構成図などに整理する。
  - システムの設置場所、本拠地はどこか
  - ネットワークやサーバー、ストレージはどのような構成にするか
- AWSのリージョン ... どこの地域に設置されているITリソースを使うか
- アベイラビリティ・ゾーン（Availability Zone: AZ） ... リージョンごとに配備されている
  - 日本国内のAZは東京、大阪の2つのリージョンがあり、その中にAZ-A、AZ-Cのように論理的な別のデータセンターがある
- AWSのサーバーは仮想サーバー
- 1つ1つの仮想サーバーは「インスタンス」と呼ぶ
- [AWS Well-Architected](https://docs.aws.amazon.com/ja_jp/wellarchitected/latest/framework/welcome.html) フレームワーク ... AWSが推奨する基本的かつ重要な事柄のチェックを行える。