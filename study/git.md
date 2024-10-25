# Git

## よく使うコマンド

| コマンド | 説明 |
| --- | --- |
| `git init` | リポジトリの初期化 |
| `git add <ファイル名>` | ステージングエリアにファイルを追加 |
| `git commit -m "<コミットメッセージ>"` | コミット |
| `git status` | 現在のステータスを表示 |
| `git log` | コミットログを表示 |
| `git log --graph` | コミットログをグラフ表示 |
| `git branch` | ブランチの一覧表示 |
| `git branch <ブランチ名>` | ブランチの作成 |
| `git branch -d <ブランチ名>` | ブランチの削除 |
| `git branch \| xargs git branch -D` | ブランチの一括削除 |
| `git checkout .` | ローカルの変更を破棄する |
| `git checkout <ブランチ名>` | ブランチの切り替え |
| `git checkout <ブランチ名> -b` | ブランチの作成と切り替え |
| `git switch <ブランチ名>` | ブランチの切り替え |
| `git switch <ブランチ名> -c` | ブランチの作成と切り替え |
| `git push origin <ブランチ名>` | リモートリポジトリにプッシュ |
| `git pull origin <ブランチ名>` | リモートリポジトリからプル |
| `git merge <ブランチ名>` | ブランチのマージ |
| `git merge origin/<ブランチ名>` | リモートブランチのマージ |
