# Go_Accessing_RDB

[Tutorial: Accessing a relational database](https://go.dev/doc/tutorial/database-access)

## イントロダクション

このチュートリアルでは、Goとその標準ライブラリのdatabase/sqlパッケージを使用してリレーショナルデータベースにアクセスするための基本を紹介します。

使用database/sqlするパッケージには、データベースへの接続、トランザクションの実行、進行中の操作のキャンセルなどのタイプと関数が含まれています。パッケージの使用の詳細については、 [データベースへのアクセス](https://go.dev/doc/database/)を参照してください。

このチュートリアルでは、データベースを作成してから、データベースにアクセスするためのコードを記述します。サンプルプロジェクトは、ビンテージジャズレコードに関するデータのリポジトリになります。

## プロジェクトモジュールの作成

```shell
mkdir Go_Accessing_RDB
cd Go_Accessing_RDB
go mod init example/data-access
```

## DB 設定

ここでは、Docker でコンテナ化した MySQL を使ってアクセスする。

database name:
```shell
mysql> create database recordings;
```

test data table:
```sql
DROP TABLE IF EXISTS album;
CREATE TABLE album (
  id         INT AUTO_INCREMENT NOT NULL,
  title      VARCHAR(128) NOT NULL,
  artist     VARCHAR(255) NOT NULL,
  price      DECIMAL(5,2) NOT NULL,
  PRIMARY KEY (`id`)
);

INSERT INTO album
  (title, artist, price)
VALUES
  ('Blue Train', 'John Coltrane', 56.99),
  ('Giant Steps', 'John Coltrane', 63.99),
  ('Jeru', 'Gerry Mulligan', 17.99),
  ('Sarah Vaughan', 'Sarah Vaughan', 34.98);
```

## データベースドライバーのインポート

[go/SQLDrivers](https://github.com/golang/go/wiki/SQLDrivers) より環境に合ったドライバーをインポートする。

ここでは、`MySQL` を利用しているので、[Go-MySQL-Driver](https://github.com/go-sql-driver/mysql/) を `main.go` のインポートに追加。


