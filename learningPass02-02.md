# 大規模なデータ分析の基礎：振り返り

## 1. 大規模なデータ分析とは

大規模なデータ分析では、大きく以下の2つの考え方がある。

### Data Warehouse
- データ分析用に最適化されたリレーショナルデータベース
- 構造化されたデータを扱う
- SQLによる分析に向いている
- BIやレポート作成などで利用する

### Data Lake
- 大量・多様なデータをファイルとして保存する
- 構造化データだけでなく、半構造化・非構造化データも扱える
- Apache Sparkなどの分散処理エンジンで処理できる
- データを読み込む際に構造を定義する「Schema on Read」が特徴

### Data Lakehouse
Data WarehouseとData Lakeの特徴を組み合わせたもの。

Data Lake
「大量・多様なデータを柔軟に保存できる」
        +
Data Warehouse
「SQL・テーブルとして分析しやすい」
        ↓
Data Lakehouse

Lakehouseを実現する重要な技術として「Delta Lake」がある。


---

# 2. 現代的な分析システムの全体像

大規模分析は、おおむね以下の流れで構成される。

データソース
    ↓
① データインジェスト・処理
    ↓
② 分析データストア
    ↓
③ 分析データモデル
    ↓
④ データ視覚化
    ↓
⑤ AI支援型分析


## ① データインジェスト・処理

さまざまなデータソースからデータを取得し、分析できる状態にする。

例：
- データベース
- ファイル
- REST API
- リアルタイムストリーム
- クラウドストレージ

ここで重要なのが「ETL」と「ELT」。


### ETL

Extract → Transform → Load

データを

1. 抽出
2. 変換
3. 保存

する。

元データ
  ↓ Extract
データ
  ↓ Transform
分析用データ
  ↓ Load
Data Warehouse


### ELT

Extract → Load → Transform

データを

1. 抽出
2. まず保存
3. 保存後に変換

する。

元データ
  ↓ Extract
データ
  ↓ Load
Data Lakeなど
  ↓ Transform
分析用データ


覚え方：

- ETL → TransformがLoadより先
- ELT → LoadがTransformより先


---

# 3. Microsoft Fabric

Microsoft Fabricは、Microsoftの統合SaaS分析プラットフォーム。

以下のような機能を統合している。

- データエンジニアリング
- データウェアハウス
- リアルタイム分析
- データサイエンス
- Power BI

これらの共通ストレージとして「OneLake」を使用する。


イメージ：

Microsoft Fabric
│
├─ Fabric Data Factory
├─ Fabric Lakehouse
├─ Fabric Warehouse
├─ Power BI
└─ その他の分析機能
        ↓
      OneLake


SaaSとして提供されるため、インフラストラクチャはMicrosoft側で運用される。


---

# 4. Azure Databricks

Azure Databricksは、Apache Sparkをベースとしたクラウド分析プラットフォーム。

主に、

- 大規模データエンジニアリング
- データサイエンス
- SQL分析

などで利用される。

コードファースト・Notebookベースの処理との親和性が高い。

主要要素：

- Databricks Lakehouse
- Databricks SQL
- Databricks Notebooks
- Unity Catalog
- Genie

ストレージ形式にはDelta Lakeが使われる。


---

# 5. データインジェスト

Data Ingestionとは、

「外部に存在するデータを分析基盤へ取り込むこと」

を指す。


データソース
├─ Azure SQL Database
├─ ファイル
├─ REST API
├─ Amazon S3
├─ Kafka
└─ IoT
      ↓
Data Ingestion
      ↓
OneLake / Lakehouse / Warehouse


Microsoft Fabricでは複数の取り込み方法が存在する。


---

# 6. Fabric Data Factory

パイプラインベースのデータ取り込み・変換に利用する。


## Pipeline

複数の処理を組み合わせて、一連のワークフローを構築する。

例：

データ取得
   ↓
データ変換
   ↓
Notebook実行
   ↓
Lakehouseへ保存

複数の「Activity」を順番または並列で実行できる。


## Dataflows Gen2

Power Queryを利用したローコードのデータ変換機能。

コードを書かずに、

- 不要列の削除
- データ型変更
- データ整形

などを行える。


整理すると、

Pipeline
→ 「処理全体をどう動かすか」

Dataflows Gen2
→ 「データそのものをどう変換するか」


---

# 7. OneLake Shortcut

外部ストレージのデータをFabricへコピーせずに参照する仕組み。

例：

Amazon S3
   ↓
Shortcut
   ↓
Fabricから参照


対応する外部ストレージの例：

- ADLS Gen2
- Amazon S3
- Google Cloud Storage
- 別のOneLakeの場所


重要：

Shortcut = データをコピーせず「参照」


---

# 8. Fabric Mirroring

外部データベースのデータを、ほぼリアルタイムでOneLakeへ継続的にレプリケートする仕組み。

例：

Azure SQL Database
        ↓
    Mirroring
        ↓
     OneLake


対象の例：

- Azure SQL Database
- Azure Cosmos DB
- Snowflake


Shortcutとの違い：

Shortcut
→ 元のデータをそのまま参照

Mirroring
→ データをOneLakeへ継続的に複製


---

# 9. Eventstream

リアルタイムのストリーミングデータを取り込むための仕組み。

例：

IoT Device
    ↓
Azure IoT Hub
    ↓
Eventstream
    ↓
フィルタ・変換
    ↓
Lakehouse / Eventhouse
    ↓
ほぼリアルタイム分析


接続可能なイベントソースの例：

- Azure Event Hubs
- Apache Kafka
- Azure IoT Hub
- カスタムエンドポイント


---

# 10. Fabric Notebook

標準的なコネクタで対応できない場合や、独自の処理が必要な場合に利用するコードベースの方法。

利用できる言語：

- PySpark
- Python
- Scala
- R
- SQL

例：

REST API
   ↓
Fabric Notebook
   ↓
Pythonによる独自処理
   ↓
Lakehouse Delta Table


---

# 11. Azure Data Factory

Fabric Data Factoryとは別に存在する、スタンドアロンのAzureデータ統合サービス。

Fabric外でデータ統合パイプラインを構築する場合に利用できる。

例：

- Azure SQL Databaseへのデータ連携
- 外部サービスへのデータ連携
- オンプレミスを含むハイブリッド環境


---

# 12. Databricksでのデータインジェスト

Azure Databricksにもデータを取り込む仕組みがある。


## Lakeflow Pipelines

Delta Lake上で増分更新パイプラインを構築するための宣言型フレームワーク。

Databricks側が、

- 実行順序
- 依存関係
- 増分処理

などを処理する。


## Databricks Notebooks

以下の言語を利用できる。

- PySpark
- Python
- Scala
- SQL
- R

REST API、JDBC、クラウドストレージ、ストリーミングソースなどからデータを読み込める。


---

# 13. 分析データストア

データを取り込んだ後は、分析データストアへ保存する。

主要な選択肢：

1. Data Warehouse
2. Data Lake
3. Data Lakehouse


---

# 14. Data Warehouseのデータモデル

Data Warehouseでは分析しやすいように、

- Fact Table
- Dimension Table

を使用する。


## Fact Table

「何が起きたか」を表す。

例：Sales

- ProductId
- CustomerId
- StoreId
- DateId
- Quantity
- Revenue

売上数量や売上金額など、分析対象となる数値を持つ。


## Dimension Table

「どの観点から分析するか」を表す。

例：

Product
- ProductId
- ProductName
- Category

Store
- StoreId
- StoreName
- Region

Time
- DateId
- Year
- Month


これによって、

- 商品別売上
- 店舗別売上
- 地域別売上
- 月別売上

などを分析できる。


---

# 15. Star Schema

Fact Tableを中心として、その周囲にDimension Tableを配置する構造。

          Product
             │
             ↓
Customer → Sales ← Store
             ↑
             │
            Time


Factを中心にDimensionが星のように配置されるため、

「Star Schema」

と呼ぶ。


---

# 16. Snowflake Schema

Dimension Tableをさらに階層化した構造。

例：

Category
   ↓
Product
   ↓
Sales


Star Schemaとの違い：

Star Schema
→ Factの周囲にDimension

Snowflake Schema
→ Dimensionをさらに関連テーブルへ分割・階層化


---

# 17. Schema on Read

Data Lakeで重要な概念。

Data Warehouseのように保存時点で厳密なスキーマを適用するのではなく、

「データを読み取るときにスキーマを定義する」

という考え方。


保存時：

CSV / JSON / Parquet / etc.
          ↓
       Data Lake


分析時：

Data Lake
    ↓
スキーマを定義
    ↓
分析


---

# 18. Delta Lake

Data Lakehouseを支える重要な技術。

Parquetファイルの上に、データベースに近い機能を追加する。

主な特徴：

- スキーマ
- トランザクションの一貫性
- バッチ読み込み
- ストリーミング
- SQLによるクエリ


イメージ：

Parquet
   ＋
スキーマ
トランザクション
SQL
   ↓
Delta Lake


「ファイルベースのData Lakeを、テーブルのように扱いやすくする」

というイメージで理解する。


Microsoft FabricとAzure Databricksの両方でDelta Lakeが利用されている。


---

# 19. Fabric LakehouseとFabric Warehouse

Microsoft Fabricでは、どちらもOneLakeをストレージ基盤として利用する。


## Fabric Lakehouse

特徴：

- Delta Lake
- Spark
- Notebook
- SQL分析エンドポイント

向いているケース：

- 多様な形式のデータを扱う
- 半構造化データも扱う
- Sparkを使う
- Notebookを使う
- データサイエンス・機械学習を行う


## Fabric Warehouse

特徴：

- リレーショナル
- T-SQL
- 強いスキーマ
- SQL中心の分析

向いているケース：

- 構造化データが中心
- SQLを中心に利用する
- 厳密なスキーマが必要


重要：

「Lakehouseが新しいからWarehouseは不要」

という関係ではない。

用途によって使い分ける。


---

# 20. Semantic Model

分析データを利用者が扱いやすくするためのモデル。

主に、

- テーブル
- リレーションシップ
- 階層
- メジャー

などを定義する。

メジャーなどの計算にはDAXを使用する。


イメージ：

Data Warehouse / Lakehouse
           ↓
     Semantic Model
           ↓
        Power BI


---

# 21. Direct Lake

Microsoft Fabricでは、Semantic ModelからOneLake上のDeltaテーブルを直接読み込む「Direct Lake」を利用できる。

OneLake
   ↓
Delta Table
   ↓
Direct Lake
   ↓
Semantic Model
   ↓
Power BI

分析のためにデータをSemantic Modelへ別途コピーせず、OneLake上のデータを読み込める。


---

# 22. データの可視化

分析したデータは、

- レポート
- ダッシュボード
- グラフ
- KPI
- 対話型の可視化

などとして利用される。

MicrosoftではPower BIが代表的。


データ
  ↓
Warehouse / Lakehouse
  ↓
Semantic Model
  ↓
Power BI
  ↓
Report / Dashboard / KPI


---

# 23. AI支援型分析

分析でもAIの活用が進んでいる。


## Microsoft Fabric / Power BI

Copilotを利用して、

- SQLクエリ作成
- DAXクエリ生成
- レポート要約
- データに関する質問

などを支援できる。


## Azure Databricks

Genieを使用して、自然言語の質問からSQLを生成・実行できる。


イメージ：

ユーザー
  ↓
「このデータについて○○を知りたい」
  ↓
Copilot / Genie
  ↓
SQLなどを生成
  ↓
データ取得・分析


---

# 24. 全体を一本につなげる

ここまでの内容を一つのデータ処理フローとして見ると分かりやすい。


【データソース】

Azure SQL Database
REST API
ファイル
Amazon S3
IoT
Kafka
など

      ↓

【データインジェスト】

Pipeline
Dataflows Gen2
Shortcut
Mirroring
Eventstream
Notebook
など

      ↓

【分析データストア】

Data Warehouse
Data Lake
Data Lakehouse

      ↓

【データモデル】

Semantic Model
Fact / Dimension
Star Schema
など

      ↓

【可視化】

Power BI
Report
Dashboard
KPI

      ↓

【AI支援分析】

Copilot
Genie


---

# 25. 最重要ポイント

## Data Warehouse / Lake / Lakehouse

Data Warehouse
→ 構造化データ + SQL + 分析

Data Lake
→ 多様なファイル + Spark + Schema on Read

Data Lakehouse
→ Lakeの柔軟性 + Warehouse的な分析機能


## ETL / ELT

ETL
→ TransformしてからLoad

ELT
→ LoadしてからTransform


## データ取り込み方法

Pipeline
→ データ移動・変換ワークフロー

Shortcut
→ データをコピーせず参照

Mirroring
→ 外部DBをOneLakeへ継続的に複製

Eventstream
→ リアルタイムイベントを取り込む

Notebook
→ コードで独自処理


## Warehouseの分析構造

Fact
→ 分析対象となる数値・出来事

Dimension
→ 分析する観点

Star Schema
→ Factを中心にDimensionを配置

Snowflake Schema
→ Dimensionをさらに階層化


## Microsoft Fabric

Fabric
  ↓
OneLake
  ├─ Fabric Lakehouse
  ├─ Fabric Warehouse
  ├─ Data Factory
  └─ Power BI

「データ取り込み → 保存 → 分析 → 可視化」までを統合した分析プラットフォームとして捉える。


---

# 26. 学習用の一言まとめ

大規模なデータ分析では、

「データを集める」
    ↓
「分析しやすい場所に保存する」
    ↓
「分析しやすい構造にする」
    ↓
「Power BIなどで可視化する」

という一連の流れを構築する。

従来はData Warehouseが中心だったが、大量・多様なデータを扱うData Lakeが登場し、
さらに両者の長所を組み合わせるData Lakehouseというアプローチが利用されている。

Microsoft FabricではOneLakeを共通ストレージとして、
Data Factory、Lakehouse、Warehouse、Power BIなどを統合し、
データの取り込みから分析・可視化までを一つのプラットフォームで扱える。
