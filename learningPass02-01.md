# Azure Cosmos DB 学習メモ

## 1. Azure Cosmos DBとは

Azure Cosmos DBは、Azureが提供するフルマネージドのNoSQLデータベースサービス。

### 主な特徴

- NoSQLデータベース
- PaaSとして提供される
- サーバー管理、パッチ、更新、バックアップなどをMicrosoftが担当
- スキーマに柔軟性がある
- 大規模なデータ処理に対応
- 低遅延
- グローバル分散に対応
- Request Unit（RU）という単位で処理コストを扱う

---

## 2. スキーマが柔軟

Azure Cosmos DBはスキーマ非依存（schema-agnostic）。

同じContainerの中でも、異なる構造のItemを保存できる。

例：

{
  "id": "book001",
  "title": "Azure入門",
  "price": 3000
}

と

{
  "id": "book002",
  "title": "Cosmos DB入門",
  "author": "Aさん",
  "tags": ["Azure", "NoSQL"],
  "rating": 4.5
}

のように、プロパティが異なるデータを同じContainerに格納できる。

RDBのように、すべてのデータが同じ列を持つ必要がない。

---

## 3. Cosmos DBのデータ構造

Cosmos DBでは、基本的に以下の階層でデータを管理する。

Cosmos DB Account
└── Database
    └── Container
        └── Item

### Account

Cosmos DBの最上位リソース。

Azure上でCosmos DBを利用するときの基本単位。

### Database

関連するContainerをまとめる論理的な単位。

例：

BookService
├── Books
├── Users
└── Reviews

### Container

データを格納・スケーリングする主要な単位。

Container単位で以下などを設定する。

- Partition Key
- Throughput
- Indexing Policy
- TTL（Time To Live）

RDBの「テーブル」と少し似ているが、完全に同じものではない。

### Item

実際に保存される個々のデータ。

利用するAPIによって、

- Document
- Row
- Node
- Edge

など呼び方が変わる。

---

## 4. Partition Key

Cosmos DBでは、大量のデータを複数の論理パーティションに分散させる。

その振り分けに利用するのが「Partition Key」。

例：

{
  "id": "book001",
  "category": "IT",
  "title": "Azure入門"
}

で、

/category

をPartition Keyとすると、categoryの値を基準としてデータを分散できる。

### Partition Keyで重要なこと

- 値の種類が十分に多い
- データが偏らず、均等に分散する

というキーを選択することが重要。

1つの論理パーティションが保持できるデータは最大20GB。

---

## 5. インデックス

Cosmos DBでは、デフォルトですべてのItemのプロパティに対してインデックスが自動的に作成・維持される。

そのため、RDBのように最初から個別にすべてのインデックスを定義する必要はない。

---

## 6. グローバル分散

Cosmos DBはグローバル分散を前提として設計されている。

Azureリージョンを追加することで、データを複数リージョンにレプリケーションできる。

マルチリージョン書き込みを有効にすると、ユーザーに近いリージョンに対して書き込みを行うこともできる。

そのため、

- グローバルサービス
- 低遅延が重要なサービス
- 高可用性が必要なサービス

などと相性が良い。

---

## 7. 整合性レベル

複数リージョンにデータをコピーすると、

「あるリージョンで書き込んだ最新データが、別リージョンですぐに読めるのか？」

という問題が発生する。

Cosmos DBでは、その整合性を5段階から選択できる。

強い
↑
Strong
Bounded Staleness
Session
Consistent Prefix
Eventual
↓
弱い

### Strong

常に最新の書き込みを読み取れる。

### Bounded Staleness

一定の時間またはバージョン数までの遅れを許容する。

### Session

同一クライアントセッション内で整合性を保証する。

広く利用されている整合性レベル。

### Consistent Prefix

書き込み順序は保証されるが、古いデータが返る可能性がある。

### Eventual

最終的にはすべてのレプリカが同じ状態になる。

---

## 8. Request Unit（RU）

Cosmos DBでは、データベース処理のコストをRequest Unit（RU）で表す。

例えば、

- 読み取り
- 書き込み
- クエリ
- 削除

といった操作でRUを消費する。

目安として、

「IDとPartition Keyを指定して1KBのItemを1件読み取る」

操作がおよそ1 RU。

そのためCosmos DBでは、

「CPUが何コア必要か？」

という考え方より、

「このデータベース処理でどれくらいRUを消費するか？」

という考え方が重要になる。

---

# Azure Cosmos DBのAPI

Cosmos DBでは、用途や既存システムに合わせて複数のAPIを利用できる。

主な5種類は以下。

Azure Cosmos DB
├── NoSQL
├── MongoDB
├── Table
├── Apache Cassandra
└── Apache Gremlin

---

## 9. Cosmos DB for NoSQL

Cosmos DBネイティブのAPI。

以前は「SQL API」と呼ばれていた。

### 特徴

- JSONドキュメントを扱う
- SQLに似たクエリ構文を利用できる
- Cosmos DBネイティブAPI

例：

SELECT *
FROM customers c
WHERE c.id = "joe@litware.com"

結果：

{
  "id": "joe@litware.com",
  "name": "Joe Jones"
}

SQLに似た構文を使うが、RDBではない。

「JSONドキュメントをSQLライクなクエリで検索するNoSQL DB」と考える。

---

## 10. Cosmos DB for MongoDB

MongoDBとの互換性を持つAPI。

### 特徴

- MongoDBドライバーを利用できる
- MongoDB Query Language（MQL）を利用
- BSON（Binary JSON）形式を使用

例：

db.products.find({ id: 123 })

### 覚え方

MongoDB
↓
BSON
↓
MongoDB Query Language

既存のMongoDBアプリケーションやMongoDBの知識を活用したい場合に関係する。

---

## 11. Cosmos DB for Table

Key-Value型のデータを扱う。

Azure Table Storageと同じプログラミングモデルを利用できる。

例：

PartitionKey | RowKey | Name
-------------|--------|-----------
1            | 123    | Joe Jones
1            | 124    | Samir Nadoy

### ポイント

PartitionKey + RowKey

の組み合わせで、各行を識別する。

### 覚え方

Table
↓
Key-Value
↓
PartitionKey + RowKey

---

## 12. Cosmos DB for Apache Cassandra

Apache Cassandraとの互換性を持つAPI。

### 特徴

- Column-familyモデル
- 同じテーブルでも各行が同じ列を持つ必要がない
- CQL（Cassandra Query Language）を使用

例：

SELECT *
FROM Employees
WHERE ID = 2

CQLはSQLと似た構文を持つ。

Apache CassandraワークロードをマネージドなクラウドDBへ移行するケースなどに適している。

---

## 13. Cosmos DB for Apache Gremlin

グラフデータを扱うAPI。

### Graphの基本概念

- Vertex（頂点・ノード）
- Edge（辺・関係）

例えば、

ユーザーA
   │
   ├── フォロー → ユーザーB
   │
   └── 所属 → 開発部

というように、データそのものだけでなく「データ同士の関係」を表現できる。

### 利用例

- ソーシャルネットワーク
- レコメンド
- 不正検知
- 組織階層

### 覚え方

Graph
↓
Vertex + Edge
↓
Gremlin

---

# 14. APIの使い分けまとめ

## NoSQL

キーワード：

- JSON
- Document
- Cosmos DBネイティブ

## MongoDB

キーワード：

- MongoDB
- BSON
- MongoDB Query Language

## Table

キーワード：

- Key-Value
- PartitionKey
- RowKey
- Azure Table Storage

## Cassandra

キーワード：

- Cassandra
- Column-family
- CQL

## Gremlin

キーワード：

- Graph
- Vertex
- Edge
- 関係性

---

# 15. なぜCosmos DBには複数のAPIがあるのか

Cosmos DB独自の操作方法だけを強制するのではなく、

- MongoDB
- Cassandra
- Table
- Gremlin

など、既存のデータモデル、クエリ言語、ライブラリ、開発者のスキルなどを活用できるようにするため。

例えば既存のMongoDBアプリケーションなら、

既存MongoDBアプリ
↓
MongoDB互換API
↓
Azure Cosmos DB

という選択肢を取れる。

---

# 16. Cosmos DBが向いているシナリオ

このモジュールでは、主に以下の用途が挙げられていた。

- IoT / テレメトリ
- ゲーム
- 小売・EC
- Webアプリ
- モバイルアプリ

共通しているポイントは、

- 柔軟なデータ構造
- 高いスケーラビリティ
- 低遅延
- グローバル分散

などが求められること。

---

# 17. Cosmos DBが向いていないケース

例えば、

- 複雑な複数テーブルJOIN
- RDB的な複雑なトランザクション処理

が重要な場合は、Azure SQL Databaseなども検討する。

また、大規模分析ワークロードについてはMicrosoft Fabricなども選択肢になる。

---

# サンプル問題のポイント

## 問1：ソーシャルネットワーク

「ソーシャルインタラクションをモデル化」

→ Apache Gremlin

理由：
人と人などの「関係」をグラフとして扱うため。

---

## 問2：BSON + MongoDBライブラリ

→ MongoDB

判断キーワード：

- BSON
- MongoDB

この2つが出たらMongoDB API。

---

## 問3：MongoDBのデータ形式

→ BSON（Binary JSON）

覚え方：

NoSQL → JSON
MongoDB → BSON

---

## 問4：Cosmos DBに向いているシナリオ

→ IoTデータの取り込みなど

Cosmos DBは、

- 大量データ
- 高スループット
- 低遅延
- スケーラビリティ

などが求められるケースと相性が良い。

---

## 問5：複数のデータモデルをサポート

→ Azure Cosmos DB

Cosmos DBでは、

NoSQL     → Document
Table     → Key-Value
Cassandra → Column-family
Gremlin   → Graph

のように複数のAPI・データモデルを利用できる。

---

## 問6：Vertex + Edge

→ Apache Gremlin

「Vertex」「Edge」が出てきたらグラフDBを表している。

Vertex + Edge
↓
Graph
↓
Gremlin

---

## 問7：Cosmos DBと最も一致しないユースケース

→ 厳密なACIDコンプライアンスを必要とする複雑なトランザクション処理

「最も一致しない」という問題文にも注意する。

- 高スループット
- 低遅延
- グローバル分散

はCosmos DBが得意。

一方、複雑なRDB的処理が中心ならAzure SQL Databaseなども検討する。

---

## 問8：パーソナライズ + SNS統合のモバイルアプリ

問題の選択肢では、

→ MongoDB

SNSという言葉だけを見てGremlinと決めないこと。

Gremlinを選ぶ重要な判断材料は、

「データ同士の関係をグラフとして表現する必要があるか」

という点。

---

## 問9：Cosmos DB for Tableのデータモデル

→ Key-Value

覚え方：

Table
↓
Key-Value
↓
PartitionKey + RowKey

---

# 試験・復習用キーワード

以下の対応関係を覚えておく。

JSON / Document
→ Cosmos DB for NoSQL

MongoDB / BSON / MQL
→ Cosmos DB for MongoDB

Key-Value / PartitionKey / RowKey
→ Cosmos DB for Table

Cassandra / Column-family / CQL
→ Cosmos DB for Apache Cassandra

Graph / Vertex / Edge / 関係性
→ Cosmos DB for Apache Gremlin

高スループット / 低遅延 / グローバル分散
→ Cosmos DBが得意

複雑なJOIN / RDB的な複雑なトランザクション
→ Azure SQL Databaseなども検討

---

# 最重要ポイントまとめ

1. Cosmos DBはAzureのフルマネージドNoSQLデータベース
2. スキーマが柔軟で、異なる構造のItemを扱える
3. Account → Database → Container → Itemという階層
4. Partition Keyによるデータ分散が重要
5. 処理コスト・性能をRUで考える
6. グローバル分散に対応
7. 5種類の整合性レベルが存在する
8. Cosmos DBでは複数のAPIを選択できる
9. NoSQL = JSONドキュメント
10. MongoDB = BSON
11. Table = Key-Value
12. Cassandra = Column-family
13. Gremlin = Graph（Vertex + Edge）
14. 高スループット・低遅延・グローバル分散が重要なシステムと相性が良い
15. 複雑なJOINなど、RDB的な処理が中心ならAzure SQL Databaseなども検討する
