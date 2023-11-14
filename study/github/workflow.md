# GitHub ワークフロー

- [ワークフローの使用 - GitHub Docs](https://docs.github.com/ja/actions/using-workflows)
- [GitHub Actions を理解する - GitHub Docs](https://docs.github.com/ja/actions/learn-github-actions/understanding-github-actions#the-components-of-github-actions)
- [GitHub Actions のワークフロー構文 - GitHub Docs](https://docs.github.com/ja/actions/using-workflows/workflow-syntax-for-github-actions)

ワークフローは自動化プロセスであり、`.github/workflows`ディレクトリ上にYAMLファイルを配置することで定義される。  
ワークフローはリポジトリ内のイベントや予め定義されたスケジュールによってトリガーされたときや、手動で実行される。  
リポジトリには複数のワークフローを定義できる。

## ワークフローの構成

ワークフローは最低限以下の指定を必要とする。

- 1つ以上のイベント（ワークフロー実行をトリガーする）
- 1つ以上のジョブ（ワークフロー内の一連のステップ）

### イベント

イベントは、ワークフロー実行をトリガーするリポジトリ内の特定のアクティビティを指す。  
イベントには以下のようなものがある。

- ワークフローのリポジトリ内で発生するイベント
- GitHubの外部で発生し、GitHubで`repository_dispatch`イベントをトリガーするイベント
- スケジュールされた時刻
- 手動実行

### ジョブ

ジョブは、同じランナーで実行される、ワークフロー内の一連のステップを指す。  
各ステップは、シェルスクリプトまたはアクションのいずれかになる。  
ステップは順番に実行され、相互に依存する。  
各ステップは同じランナーで実行されるため、ステップ間でデータを共有できる。  
既定のジョブに依存関係はなく並列に実行されるが、依存関係が定義されたジョブは同期的に実行される。