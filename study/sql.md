# SQL

## データ型

### 数値型

- `bigint` : 整数、範囲は -9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807
- `int` : 整数、範囲は -2,147,483,648 ～ 2,147,483,647
- `mediumint` : 整数、範囲は -8,388,608 ～ 8,388,607
- `smallint` : 整数、範囲は -32,768 ～ 32,767
- `tinyint` : 整数、範囲は 0 ～ 255
- `bit` : 整数、範囲は 0 または 1
- `decimal` : 固定長桁数、固定長少数桁数を持つ数値
- `money` : 通貨
- `float` : 浮動小数点数値

### 文字列型

- `char` : 8000 文字以内の固定長の文字列
- `nchar` : 4000 文字以内の固定長の文字列(UNICODE)
- `varchar` : 8000 文字以内の可変長の文字列
- `nvarchar` : 4000 文字以内の可変長の文字列(UNICODE)
- `text` : 20 億文字以内の可変長の文字列
- `ntext` : 10 億文字以内の可変長の文字列(UNICODE)

### 日付型

- `datetime` : 日付

## CREATE 文

DB そのものや、テーブルなどの DB 上のオブジェクトを作成する。

```
CREATE TABLE table_name (
  id BIGINT NOT NULL AUTO_INCREMENT,
  name VARCHAR(60) NOT NULL,
  PRIMARY KEY (id)
) ENGINE = InnoDB
;
```

- `AUTO_INCREMENT` : レコードの識別子となるユニークな値を生成できる
- `PRIMARY KEY (id)` : レコードの識別子となるユニークな値を指定する
- `ENGINE` : 利用するストレージエンジンを指定する。省略するとデフォルトのストレージエンジン (InnoDB) が使用される

## INSERT 文

既存のテーブルに新しい行を追加する。

```
INSERT INTO table_name SET
  name 'Taro Yamada'
;
```

## SELECT 文

1つもしくは複数のテーブルからデータを抽出する。

```
SELECT name AS person_name FROM table_name WHERE name = 'Taro Yamada'
;
```

- `AS` : 取得したデータの列名を別の名称に変更する
- `WHERE` : 条件式を用いてデータを絞り込む
