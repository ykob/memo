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

- [テンプレートの構造分析 - AWS CloudFormation](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/template-anatomy.html)

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

スタックを作成するとき、更新するときにテンプレートに渡されるパラメータ、またはパラメータの組み合わせを検証する。  
テンプレートルールを使用するには、テンプレートに Rules セクションを追加し、そのセクションにアサーションを記述する。

各テンプレートルールは 2 つのプロパティで構成されている。

- RuleCondition: ルールの条件を記述する。ルールの条件は、テンプレートのパラメータとその値に基づいている。
- Assertions: ルールの条件が満たされているかどうかを検証するアサーションを記述する。

それぞれのルールに定義できる条件は 1 つだけである。

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

## Mappings

Mappings セクションは、キーと関連する値のマッピングで、条件パラメータ値の指定に使用する。

```yaml
Mappings:
  RegionMap:
    us-east-1:
      "HVM64": "ami-0ff8a91507f77f867"
    us-west-1:
      "HVM64": "ami-0bdb828fd58c52235"
    eu-west-1:
      "HVM64": "ami-047bb4163c506cd98"
    ap-southeast-1:
      "HVM64": "ami-08569b978cc4dfa10"
    ap-northeast-1:
      "HVM64": "ami-06cd52961ce9f0d85"
```

### マッピングから値を返す

`Fn::FindInMap` 関数を使用して、マッピングから値を返すことができる。  
以下の例では Amazon EC2 インスタンスリソースの `ImageId` プロパティの値を `FindInMap` 関数で指定している。

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Mappings:
  RegionMap:
    us-east-1:
      HVM64: ami-0ff8a91507f77f867
      HVMG2: ami-0a584ac55a7631c0c
    us-west-1:
      HVM64: ami-0bdb828fd58c52235
      HVMG2: ami-066ee5fd4a9ef77f1
    eu-west-1:
      HVM64: ami-047bb4163c506cd98
      HVMG2: ami-0a7c483d527806435
    ap-northeast-1:
      HVM64: ami-06cd52961ce9f0d85
      HVMG2: ami-053cdd503598e4a9d
    ap-southeast-1:
      HVM64: ami-08569b978cc4dfa10
      HVMG2: ami-0be9df32ae9f92309
Resources:
  myEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
      InstanceType: m1.small
```

マップ内の特定の値を参照するために入力パラメータを使用することもできる。  
以下の例では、`EnvironmentType` パラメータの値を使用して、`RegionAndInstanceTypeToAMIID` マップ内の特定の値を参照している。

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Parameters:
  EnvironmentType:
    Description: The environment type
    Type: String
    Default: test
    AllowedValues:
      - prod
      - test
    ConstraintDescription: must be a prod or test
Mappings:
  RegionAndInstanceTypeToAMIID:
    us-east-1:
      test: "ami-8ff710e2"
      prod: "ami-f5f41398"
    us-west-2:
      test: "ami-eff1028f"
      prod: "ami-d0f506b0"
Resources: ...other resources...
Outputs:
  TestOutput:
    Description: Return the name of the AMI ID that matches the region and environment type keys
    Value:
      !FindInMap [
        RegionAndInstanceTypeToAMIID,
        !Ref "AWS::Region",
        !Ref EnvironmentType,
      ]
```

## Conditions

条件を作成し、それをリソースや出力に関連付けることで、条件が true の場合にのみリソースや出力を作成するようにできる。

以下の例では、CreateProdResources という条件は EnvType パラメータが prod の場合に true になる。

```yaml
Conditions:
  CreateProdResources: !Equals
    - !Ref EnvType
    - prod
```

以下の例では、CreateBucketPolicy という条件は別の条件である IsProduction と CreateBucket を参照しており、両方が true の場合に true になる。

```yaml
Conditions:
  IsProduction: !Equals
    - !Ref EnvType
    - prod
  CreateBucket: !Not
    - !Equals
      - !Ref BucketName
      - ""
  CreateBucketPolicy: !And
    - !Condition IsProduction
    - !Condition CreateBucket
```

## Transform

AWS CloudFormation がテンプレートを処理するために使用するマクロを 1 つ以上指定する。  
マクロは、テンプレートの構文を拡張するために使用される。

以下の例では、​​AWS CloudFormation が MyMacro を評価し、その後 AWS::Serverless を評価する。  
どちらも Transform セクションに含まれているため、テンプレート全体のコンテンツを処理できる。

```yaml
Transform:
  - MyMacro
  - 'AWS::Serverless'
Resources:
  WaitCondition:
    Type: 'AWS::CloudFormation::WaitCondition'
  MyBucket:
    Type: 'AWS::S3::Bucket'
    Properties:
        BucketName: MyBucket
        Tags: [{"key":"value"}]
        CorsConfiguration:[]
  MyEc2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
        ImageID: "ami-123"
```
