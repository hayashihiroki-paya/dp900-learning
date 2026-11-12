DP-900：Azure Cosmos DB の API と互換性

1. 結論

Azure Cosmos DB は NoSQL データベースサービスであり、MongoDB や Cassandra などの既存データベースと互換性のある API を提供している。

そのため、既存のアプリケーションを大幅に変更せずに Azure Cosmos DB へ移行しやすい。

DP-900 では、次の違いを覚える。

- API for NoSQL：Azure Cosmos DB のネイティブ API
- API for MongoDB：MongoDB との互換性を提供
- API for Cassandra：Apache Cassandra との互換性を提供
- API for Table：Azure Table Storage との互換性を提供

試験のポイント：既存アプリケーションを少ないコード変更で移行する場合は、互換性 API を選ぶ。

---

2. Azure Cosmos DB とは

Azure Cosmos DB は、Microsoft Azure が提供するフルマネージドの NoSQL データベースサービス。

特徴：

- JSON ドキュメントなどのデータを保存できる
- グローバル分散に対応している
- 高いスケーラビリティを提供する
- 複数のデータベース API をサポートする

API の種類と役割

API| 分類| 主な用途
API for NoSQL| ネイティブ API| Cosmos DB 向けのアプリケーション開発
API for MongoDB| 互換性 API| MongoDB アプリケーションの移行
API for Cassandra| 互換性 API| Cassandra アプリケーションの移行
API for Table| 互換性 API| Azure Table Storage アプリケーションの移行
API for Gremlin| 互換性 API| Gremlin を使用したグラフデータの操作

注意：MongoDB や Cassandra も NoSQL データベースである。

「API for NoSQL」という名前は、他の API が NoSQL ではないという意味ではない。

---

3. 互換性 API による移行の仕組み

例えば、MongoDB を使用しているアプリケーションを Azure Cosmos DB に移行する場合を考える。

移行前

アプリケーション
      |
      | MongoDB の通信プロトコル
      v
    MongoDB

移行後

アプリケーション
      |
      | MongoDB の通信プロトコル
      v
Azure Cosmos DB
 (API for MongoDB)

Azure Cosmos DB が MongoDB と互換性のある通信プロトコルをサポートするため、既存の MongoDB ドライバーやアプリケーションコードを再利用しやすい。

メリット

1. 既存アプリケーションのコード変更を減らせる
2. 使い慣れたドライバーやクエリを継続して利用できる
3. Azure のマネージドデータベース環境へ移行しやすくなる

ただし、API の互換性とデータの移行は別の話。

互換性 API を選択しても、データが自動的に移行されるわけではない。データ移行ツールなどを使用する必要がある。

また、すべての機能が完全に互換とは限らないため、一部のコード修正が必要になる場合もある。

---

4. MongoDB と Cassandra はオープンソースなのか？

項目| MongoDB| Apache Cassandra
データベースの種類| NoSQL| NoSQL
主なデータモデル| ドキュメント型| ワイドカラム型
ソースコード| 公開されている| 公開されている
ライセンス| SSPL| Apache License 2.0
オープンソースの位置付け| 厳密には OSI 認定のオープンソースではない| オープンソース
Cosmos DB の対応 API| API for MongoDB| API for Cassandra

MongoDB

MongoDB は、JSON に似たドキュメント形式でデータを管理する NoSQL データベース。

ソースコードは公開されているが、現在の SSPL ライセンスは OSI のオープンソース定義に該当しない。

Apache Cassandra

Apache Cassandra は、大量のデータを分散して管理する NoSQL データベース。

Apache License 2.0 を採用しているオープンソースソフトウェア。

DP-900 では、ライセンスの違いよりも、それぞれが Cosmos DB とは別のデータベースであり、互換性 API が用意されていることが重要。

---

5. DP-900 練習問題の解説

問題

既存のアプリケーションを最小限のコード変更で Azure Cosmos DB に移行できる API はどれか。（複数選択）

- A. API for MongoDB
- B. API for Cassandra
- C. API for Table
- D. API for NoSQL

正解

A・B・C

選択肢| 正誤| 理由
A. MongoDB| 正解| MongoDB の通信プロトコルとの互換性がある
B. Cassandra| 正解| Cassandra の通信プロトコルや CQL に対応
C. Table| 正解| Azure Table Storage の API と互換性がある
D. NoSQL| 不正解| Cosmos DB のネイティブ API である

※ 問題文が「広く使われるオープンソースデータベースとの互換性」に限定している場合、C の Table は厳密には該当しない。Table API は Azure Table Storage との互換性を提供するものである。

---

6. 試験での覚え方

「NoSQL は Cosmos DB 自身の API、MongoDB・Cassandra・Table は既存システムとの互換性 API」

問題文に以下のキーワードが出たら、互換性 API を考える。

- 既存アプリケーションの移行
- 最小限のコード変更
- 既存のドライバーを再利用
- 既存の通信プロトコルやクエリ言語を利用

一方、Cosmos DB のネイティブな機能を利用してアプリケーションを開発する場合は、API for NoSQL を考える。

---

7. Microsoft 公式ドキュメント

- "Azure Cosmos DB の概要" (https://learn.microsoft.com/ja-jp/azure/cosmos-db/)
- "Azure Cosmos DB for MongoDB" (https://learn.microsoft.com/en-us/azure/documentdb/documentdb-protocol-mongodb)
- "Azure Cosmos DB for Apache Cassandra" (https://learn.microsoft.com/en-us/azure/cosmos-db/cassandra-introduction)
- "Azure Cosmos DB for Table" (https://learn.microsoft.com/en-us/azure/cosmos-db/table/overview)

---

最重要ポイント

Azure Cosmos DB は、他のデータベースの通信方式や API と互換性を持つことで、既存アプリケーションを移行しやすくしている。

ただし、互換性 API はデータを自動移行する機能ではない。

DP-900 では、ネイティブ API と互換性 API の違いを理解しておく。