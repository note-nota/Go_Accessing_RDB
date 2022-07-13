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

## データベースハンドルを取得して接続

`*sql.DB` : DB データベースハンドル

ここでは単純化のためにグローバル変数にしてるが、本番環境では変数として渡すかまたは `struct` でラップするなどして、グローバル変数を避けた実装をすべき。

DB に接続できることを `DB.Ping` で確認。

## 複数レコードの返却

レコードの構造体の定義

```go
type Album struct {
    ID     int64
    Title  string
    Artist string
    Price  float32
}
```

- `DB.Query` で複数行の返却
- 構造体のフィールド名とタイプは、データベースの列の名前とタイプに対応しています。 
- ループの後、`rows.Err` を使用して、クエリ全体からのエラーを確認します。クエリ自体が失敗した場合、ここでエラーをチェックすることが、結果が不完全であることを確認する唯一の方法であることに注意してください。(?)

## 単一行が返ってくることがわかってるクエリ

- `DB.QueryRow` で単一行の返却
- ここではコードの単純化のために `DB.QueryRow` からのエラーを処理せずに、`Row.Scan` 時に、`sql.ErrNoRows` をチェックしてます。

## データの追加

- `INSERT` ステートメントを実行するために `DB.Exec` 使用
- `Result.LastInsertId` 挿入されたデータベース行のIDを取得


