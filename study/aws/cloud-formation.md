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

| セクション               | 必須/任意 | 説明                                                                 |
| ------------------------ | --------- | -------------------------------------------------------------------- |
| AWSTemplateFormatVersion | 必須      | テンプレートが準拠している AWS CloudFormation テンプレートバージョン |
| Description              | 任意      | テンプレートの説明                                                   |
| Metadata                 | 任意      | テンプレートに関する追加情報を提供するオブジェクト                   |
| Parameters               | 任意      | 実行時（スタックを作成・更新するとき）にテンプレートに渡す値         |
| Rules                    | 任意      | テンプレートの構文を検証する                                         |
| Mappings                 | 任意      | キーと関連する値のマッピングで、条件パラメータ値の指定に使用する     |
| Conditions               | 任意      | リソースの作成や更新を制御する条件                                   |
| Transform                | 任意      | テンプレートの内容を変換する                                         |
| Resources                | 必須      | EC2 インスタンス、S3 バケットなどのスタックリソースとそのプロパティ  |
| Outputs                  | 任意      | スタックのプロパティを確認すると返される値                           |

## AWSTemplateFormatVersion

テンプレートが準拠している AWS CloudFormation テンプレートバージョンを記述する。  
最新のテンプレートの形式バージョンは 2010-09-09 であり、現時点で唯一の有効な値となる。  
値を指定しない場合、AWS CloudFormation は最新のテンプレートの形式バージョンを使用する。

```yaml
AWSTemplateFormatVersion: "2010-09-09"
```

## Description

テンプレートの説明を記述する。

```yaml
Description: "AWS CloudFormation Sample Template"
```

## Metadata

テンプレートに関する追加情報を提供するオブジェクトを記述する。

```yaml
Metadata:
  Instances:
    Description: "Information about the instances"
  Databases:
    Description: "Information about the databases"
```

## Parameters

パラメータを使用することで、スタックを作成するまたは更新するたびにテンプレートに値を渡すことができる。

```yaml
Parameters:
  InstanceTypeParameter:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - m1.small
      - m1.large
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.
```

### テンプレート内でのパラメータの参照

パラメータを参照するには、パラメータの名前を `!Ref` 関数で囲む。  
同じテンプレートの Resources および Outputs セクションのパラメータを参照できる。

```yaml
Resources:
  EC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceTypeParameter
```

## Rules

- [[Rules] (ルール) - AWS CloudFormation](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/rules-section-structure.html)

スタックを作成するとき、更新するときにテンプレートに渡されるパラメータ、またはパラメータの組み合わせを検証する。  
テンプレートルールを使用するには、テンプレートに Rules セクションを追加し、そのセクションにアサーションを記述する。

各テンプレートルールは2つのプロパティで構成されている。

- RuleCondition: ルールの条件を記述する。ルールの条件は、テンプレートのパラメータとその値に基づいている。
- Assertions: ルールの条件が満たされているかどうかを検証するアサーションを記述する。

それぞれのルールに定義できる条件は1つだけである。

```yaml
Rules:
  Rule01:
    RuleCondition:
      rule-specific intrinsic function: Value01
    Assertions:
      - Assert:
          rule-specific intrinsic function: Value02
        AssertDescription: Information about this assert
      - Assert:
          rule-specific intrinsic function: Value03
        AssertDescription: Information about this assert
  Rule02:
    Assertions:
      - Assert:
          rule-specific intrinsic function: Value04
        AssertDescription: Information about this assert
```

