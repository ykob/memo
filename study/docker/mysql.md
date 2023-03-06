# Docker で MySQL を構築する

## `my.cnf`

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
