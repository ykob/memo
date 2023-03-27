# Docker で PHP + MySQL の環境を構築する

## Commands

### `docker exec`

実行中のコンテナ内で新しいコマンドを実行する。  
抜ける際には `exit` を実行する。

```
docker exec -it [container-name] bash
```

## PHP

### Dockerfile

`mysqli` などのモジュールのインストールは Dockerfile に記述しておく。

```
FROM php:7.2-apache
COPY ./php.ini /usr/local/etc/php/
RUN apt-get update && docker-php-ext-install mysqli
```

### MySQL に接続する

- `mysqli_connect` : 新規にMySQLサーバーへの接続をオープンする。
- `mysqli_select_db` : クエリを実行するための標準のデータベースを選択する。
- `mysqli_query` : データベース上でクエリを実行して、結果を返す。
- `mysqli_fetch_assoc` : クエリ実行の結果セットから１行取得し、連想配列として返す。
- `mysqli_fetch_array` : クエリ実行の結果セットから１行取得し、連想配列か数値添字配列、あるいはその両方として返す。
- `mysqli_close` : 既に開いているデータベース接続を閉じる。

```
<?php
  $mysqli = mysqli_connect('mysql', 'test', 'test', 'test');
  mysqli_select_db($mysqli, 'test');
  $data = mysqli_query($mysqli, 'SELECT name FROM person');

  while ($row = mysqli_fetch_assoc($data)) {
    print $row['name'];
  }

  mysqli_close($mysqli);
?>
```

### MySQL を更新する

- `mysqli_prepare` : 実行するSQLステートメントを準備する。
  - ステートメントオブジェクトを返す。
  - エラー時には `false` を返す。
- `mysqli_stmt_bind_param` : プリペアドステートメントのパラメータに変数をバインドする。
  - 第二引数にはバインドする変数の型を指定する。長さはステートメント中のパラメータ数と一致させる。
    - `i` : int
    - `d` : float
    - `s` : string
    - `b` : blob
- `mysqli_stmt_execute` : プリペアドステートメントを実行する。

```
<?php
  $stmt = mysqli_prepare($mysqli, 'UPDATE person SET name = ?');
  mysqli_stmt_bind_param($stmt, 's', 'Taro Tanaka');
  mysqli_stmt_execute($stmt);
?>
```

## MySQL

### `my.cnf`

`my.cnf` は MySQL 起動時の設定ファイル。  
以下のようにして文字のエンコード指定などを記述できる。

```
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci

[client]
default-character-set=utf8mb4
```

Docker で MySQL を構築する場合は、Dockerfile 内でファイルの場所と追加先を指定し、パーミッションを 644 に設定する。

```
FROM mysql:latest
ADD ./env/my.cnf /etc/mysql/conf.d/my.cnf
RUN chmod 644 /etc/mysql/conf.d/my.cnf
```

### `docker-entrypoint-initdb.d`

Docker の MySQL イメージでは `docker-entrypoint-initdb.d` というディレクトリ内にスクリプトのファイルを配置しておくと、MySQL イメージがコンテナを作成・起動する際にそれらを実行する。
データベースのテーブルやデータを作成する SQL ファイルを配置することで、MySQL コンテナの生成・起動時にファイルにデータを作成することができる。

```
version: "3"

services:
  mysql:
    image: mydql:latest
    environment:
      MYSQL_ROOT_PASSWORD: root
      TZ: "Asia/Tokyo"
    volumes:
      - ./init:/docker-entrypoint-initdb.d
```

### PHPからMySQLを接続する際に `SQLSTATE[HY000] [2054]` のエラーを回避する

MySQL8.0.4以降からログイン認証方式の標準が `caching_sha2_password` に変更された。  
しかしPHPのMySQL接続モジュールが `caching_sha2_password` に未対応の場合がある。  
これにより、Docker環境下のMySQLにPHPから `mysqli_connect` で接続しようとすると以下のようなエラーになる。

```
SQLSTATE[HY000] [2054] The server requested authentication method unknownto the client
```

このため、MySQLのログイン認証方式を `mysql_native_password` に変更する必要がある。  
`docker-compose.yml` のMySQLイメージ欄以下の `command` に `--default-authentication-plugin=mysql_native_password` を追加してあげればよい。

## Laravel

### Commands

- `artisan` : Laravel が提供するコマンド
- `artisan serve` : 開発サーバーを起動する
- `artisan make:model {ModelName}` : `ModelName` に指定した名前でモデルを新規作成する
  - `-a`, `--all` : モデルのマイグレーション、シーダー、ファクトリー、フォームリクエスト、ポリシー、コントローラーを新規作成する
  - `-c`, `--controller` : モデルのコントローラーを新規作成する 
  - `-m`, `--migration` : モデルのマイグレーションファイルを新規作成する
- `artisan make:migration {MigrationFileName}` : `MigrationFileName` に指定した名前でマイグレーションファイルを新規作成する