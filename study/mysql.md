# MySQL

## WSL環境にMySQLをインストールする

1. パッケージ一覧を更新する

```
sudo apt update
```

2. MySQLをインストールする

```
sudo apt install mysql-server
```

3. MySQLのコマンドを実行できるかどうかを確認する

```
mysql --version
```

## MySQLに接続する

```
mysql -h 127.0.0.1 -u root -p
```

- `-h 127.0.0.1` ... 接続先のホスト（サーバー）のIPアドレスを指定する。
- `-u root` ... MySQLのユーザー名をrootとしてログインする。
- `-p` ... パスワードを入力するプロンプトを表示する。

## よく使うMySQLコマンド

| コマンド | 説明 |
| --- | --- |
| `USE DATABASE_NAME` | MySQLで使用するデータベースを選択する。 |
| `SHOW COLUMNS FROM Model;` | MySQLのテーブル `Model` のカラム（列）の情報を表示する。 |
| `exit;` | MySQLの対話型コマンドライン（MySQLシェル）を終了し、通常のターミナル（シェル）に戻る。 |
