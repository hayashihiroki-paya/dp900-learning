# データサービスのまとめ（Azure / Fabric / Purview）

## 1. Azure SQL ファミリー（リレーショナルデータベース）
### 種類と特徴
- **Azure SQL Database**  
  - 完全マネージド（PaaS）  
  - 最小限の管理で運用可能  
- **Azure SQL Managed Instance**  
  - SQL Server 互換性が高い  
  - 柔軟性と管理負荷のバランス型  
- **Azure SQL VM**  
  - 仮想マシン上で SQL Server を完全管理  
  - **最大の構成自由度と管理責任**

### 主な利用者
- データベース管理者（DBA）
- データエンジニア（ETL のソースとして）
- データアナリスト（直接クエリする場合も）

---

## 2. Azure オープンソースデータベース
- **Azure Database for MySQL**  
  LAMP スタックで一般的なオープンソース DB
- **Azure Database for PostgreSQL**  
  リレーショナル＋オブジェクトのハイブリッド DB

### 主な利用者
- DBA  
- データエンジニア  
- データアナリスト  

---

## 3. Azure Cosmos DB（NoSQL）
### 特徴
- グローバル分散  
- JSON ドキュメント、キー値、グラフなどを保存  
- 超高速読み書き  
- 多様な API（Core、MongoDB、Cassandra など）

### 主な利用者
- 開発者  
- データエンジニア  
- データアナリスト  

---

## 4. Azure Storage（データレイクの基盤）
### 種類
- **Blob Storage**（ファイル保存・データレイク）
- **File Share**
- **Table Storage**（キー値ストア）

### 主な利用者
- データエンジニア（データレイク構築）
- AI エンジニア（学習データ保存）

---

## 5. Azure Data Factory（ETL / パイプライン）
### 特徴
- データの抽出・変換・読み込み（ETL）
- スケジュール実行・監視
- オンプレとクラウドを接続可能

### 主な利用者
- **データエンジニア**

---

## 6. Microsoft Fabric（統合分析プラットフォーム）
### 含まれる機能
- Fabric Data Factory（ETL）
- Lakehouse（データレイク＋SQL）
- Warehouse（データウェアハウス）
- Real-Time Analytics
- Data Science / ML
- Power BI
- ガバナンス（Purview と連携）
- OneLake による統合ストレージ

### 主な利用者
- データエンジニア  
- データアナリスト  
- AI エンジニア  

---

## 7. Fabric IQ（プレビュー）
### 特徴
- OneLake 全体のデータを「共通の意味」で統合  
- Customer / Order / Product などの定義を統一  
- AI エージェントが自然言語でデータを理解できるようにする

---

## 8. Power BI（BI・可視化）
### 特徴
- レポート・ダッシュボード作成  
- セマンティックモデルでデータを構造化  
- Fabric と統合可能

### 主な利用者
- データアナリスト  
- ビジネスユーザー  

---

## 9. Azure Databricks（Sparkベースのレイクハウス）
### 特徴
- 大規模データ処理  
- Delta Lake によるレイクハウス  
- ノートブックで分析可能

### 主な利用者
- データエンジニア  
- データアナリスト  

---

## 10. Azure Stream Analytics（リアルタイム処理）
### 特徴
- IoT やログのストリーミングデータをリアルタイム処理  
- 分析ストアやダッシュボードに即時反映

### 主な利用者
- データエンジニア

---

## 11. Azure Data Explorer（ログ・テレメトリ分析）
### 特徴
- 時系列データの高速クエリ  
- IoT・ログ・テレメトリ分析に最適

### 主な利用者
- データアナリスト

---

## 12. Microsoft Purview（データガバナンス）
### 特徴
- データカタログ  
- データ分類  
- データリネージ（系統）  
- コンプライアンス管理

### 主な利用者
- データエンジニア  
- ガバナンス担当  

---

## 13. Microsoft Foundry（AIアプリ開発基盤）
### 特徴
- LLM アプリ開発  
- マルチエージェント  
- AI パイプライン  
- チャットオーバーデータ

### 主な利用者
- AI エンジニア  
- 開発者  

---

# 🎯 まとめ（要点）
- **OLTP（トランザクション）**：Azure SQL / MySQL / PostgreSQL / Cosmos DB  
- **OLAP（分析）**：Fabric / Databricks / Data Explorer / Stream Analytics  
- **ETL・統合**：Data Factory / Fabric Data Factory  
- **ガバナンス**：Purview  
- **可視化**：Power BI  
- **AI 統合**：Foundry / Fabric / Cosmos DB  

Azure と Fabric のデータサービスは、  
**「保存 → 取り込み → 変換 → 分析 → ガバナンス → AI」**  
の全工程をカバーするように設計されています。
