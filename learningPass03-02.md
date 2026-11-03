# DP-900 学習メモ：Power BI のデータモデリングとデータ視覚化

## 1. 今回の学習内容の全体像

Power BI によるデータ分析は、大きく次の流れで考える。

```mermaid
flowchart TD
    A["元データ"] --> B["セマンティックモデル"]
    B --> C["メジャー<br/>何を分析するか"]
    B --> D["ディメンション<br/>どの切り口で分析するか"]

    C --> E["集計・分析"]
    D --> E

    E --> F["データ視覚化"]
    F --> G["Power BI レポート"]

    F --> H["テーブル"]
    F --> I["カード"]
    F --> J["棒グラフ"]
    F --> K["折れ線グラフ"]
    F --> L["円グラフ"]
    F --> M["散布図"]
    F --> N["マップ"]
```

つまり、

> データを用意する  
> → データの意味や関係を定義する  
> → 何を・どの切り口で分析するか決める  
> → 集計する  
> → 分かりやすく視覚化する

という流れ。

---

# 2. セマンティックモデル

## セマンティックモデルとは

元データを分析できるように構造化し、

- 各データが何を意味しているのか
- データ同士がどのような関係なのか
- どの数値を分析するのか
- どのような切り口で分析するのか

などを定義した**分析用のデータモデル**。

「semantic」は「意味」という意味。

そのため、

> **セマンティックモデル = データを分析可能なように構造化し、それぞれのデータの意味やデータ同士の関係を明確にしたもの**

と考える。

例えば、次のような関係を定義する。

```mermaid
flowchart TD
    Sales["Sales<br/>売上"]
    Product["Product<br/>商品"]
    Customer["Customer<br/>顧客"]
    Date["Date<br/>日付"]

    Product --> Sales
    Customer --> Sales
    Date --> Sales
```

単にテーブルが存在するだけではなく、

```text
Sales    → 売上を表す
Product  → 商品を表す
Customer → 顧客を表す
Date     → 日付を表す

さらに、それぞれのテーブルが
どのように関連しているかを定義する。
```

---

# 3. メジャーとディメンション

## メジャー（Measure）

**分析対象となる数値。**

例：

- 売上金額
- 販売数量
- 利益

覚え方：

> **「何を分析するか？」= メジャー**

---

## ディメンション（Dimension）

**メジャーをどのような切り口で集計・分析するかを表すもの。**

例：

- 商品
- 顧客
- 地域
- 時間

例えば「商品別の売上金額」なら、

```mermaid
flowchart LR
    A["売上金額<br/>Measure"] --> C["商品別売上"]
    B["商品<br/>Dimension"] --> C
```

```text
売上金額
→ 分析対象
→ メジャー

商品
→ 分析の切り口
→ ディメンション
```

### 試験向けの覚え方

> **Measure = 何を？**

> **Dimension = 何別に？**

---

# 4. ファクトテーブルとディメンションテーブル

## ファクトテーブル

分析対象となるイベントや数値を保持するテーブル。

例：

```text
Sales

日付ID | 商品ID | 顧客ID | 数量 | 売上金額
-------|--------|--------|------|---------
001    | P001   | C001   | 2    | 100000
002    | P002   | C001   | 1    |  50000
003    | P001   | C002   | 3    | 150000
```

販売数量や売上金額など、分析対象となる数値を含む。

---

## ディメンションテーブル

分析するときの切り口となる情報を保持する。

例えば Product テーブルなら、

```text
商品ID | 商品名   | カテゴリ
-------|----------|----------
P001   | Surface  | PC
P002   | Xbox     | Game
```

覚え方：

```text
Fact
→ 「何が起きた？」

Dimension
→ 「何について分析する？」
```

---

# 5. スタースキーマ

ファクトテーブルを中心として、1つ以上のディメンションテーブルが関連する構造。

```mermaid
flowchart TD
    Product["Product<br/>Dimension"]
    Customer["Customer<br/>Dimension"]
    Date["Date<br/>Dimension"]
    Store["Store<br/>Dimension"]

    Sales["Sales<br/>Fact"]

    Product --- Sales
    Customer --- Sales
    Date --- Sales
    Store --- Sales
```

ここでは、

```text
Sales
→ Fact Table

Product
Customer
Date
Store
→ Dimension Table
```

中央のファクトテーブルを中心にディメンションが広がるため、

> **Star Schema**

と呼ばれる。

---

# 6. スノーフレークスキーマ

ディメンションテーブルが、さらに別の詳細テーブルと関連する構造。

```mermaid
flowchart TD
    Category["Category"]
    Product["Product<br/>Dimension"]
    Customer["Customer<br/>Dimension"]
    Date["Date<br/>Dimension"]
    Sales["Sales<br/>Fact"]

    Category --- Product
    Product --- Sales
    Customer --- Sales
    Date --- Sales
```

ここでは Product がさらに Category と関連している。

## Star と Snowflake の違い

```mermaid
flowchart LR
    subgraph Star["Star Schema"]
        SF["Fact"] --- SD1["Dimension"]
        SF --- SD2["Dimension"]
        SF --- SD3["Dimension"]
    end

    subgraph Snowflake["Snowflake Schema"]
        NF["Fact"] --- ND1["Dimension"]
        NF --- ND2["Dimension"]
        ND1 --- Detail["詳細テーブル"]
    end
```

重要：

> **Snowflake = より細かく分析するという意味ではない。**

> **Dimension がさらに別の詳細テーブルと関連している構造。**

---

# 7. 階層（Hierarchy）

ディメンションには階層を定義できる。

例えば時間なら、

```mermaid
flowchart TD
    Year["年"] --> Month["月"] --> Day["日"]
```

商品なら、

```mermaid
flowchart TD
    Category["カテゴリ"] --> Product["商品"]
```

---

## ドリルダウン

大きな単位から小さな単位へ掘り下げる。

```mermaid
flowchart TD
    Year["2026年"] --> Month["11月"] --> Day["11月3日"]
```

```text
年
↓
月
↓
日
```

---

## ドリルアップ

ドリルダウンの逆。

```text
日
↓
月
↓
年
```

詳細なレベルから大きな単位へ戻る。

### 覚え方

```text
Drill Down
大 → 小

Drill Up
小 → 大
```

---

# 8. VertiPaq

## VertiPaqとは

Power BI で Import モードを使用した場合、セマンティックモデルにインポートされたデータは VertiPaq によって、

> **インメモリの列指向ストア**

に格納される。

```mermaid
flowchart TD
    A["データソース"]
    B["Power BI<br/>Semantic Model"]
    C["VertiPaq"]
    D["RAM<br/>インメモリ"]
    E["列指向でデータを保持"]
    F["高速な集計・分析"]
    G["Power BI レポート"]

    A -->|Import| B
    B --> C
    C --> D
    D --> E
    E --> F
    F --> G
```

VertiPaq は、

```text
インメモリ
+
列指向
```

という2つに分けて理解すると分かりやすい。

---

# 9. インメモリ

データを RAM（メモリ）上で扱うこと。

```text
インメモリ
= どこでデータを扱うか
= RAM
```

メモリ上のデータを効率的に処理することで、高速な分析につながる。

---

# 10. 列指向

例えば次のデータがある。

```text
日付       商品       売上金額
11/01      Surface    100000
11/01      Xbox        50000
11/02      Surface    120000
```

行単位で見ると、

```text
11/01, Surface, 100000
11/01, Xbox,      50000
11/02, Surface, 120000
```

1行には、

- 日付
- 商品
- 金額

という異なる種類のデータが存在する。

列指向では、概念的には列単位で扱う。

```text
日付
11/01
11/01
11/02

商品
Surface
Xbox
Surface

売上金額
100000
50000
120000
```

例えば、

> 「売上金額の合計を求める」

なら、

```text
売上金額
100000
 50000
120000
   ↓
 SUM
   ↓
270000
```

のように売上金額の列を使って集計する。

そのため、

- 合計
- 平均
- 最大値
- 最小値

などの分析で、必要となる列を効率的に処理できる。

## 整理

```mermaid
flowchart TD
    V["VertiPaq"]

    V --> M["インメモリ"]
    V --> C["列指向"]

    M --> M2["RAM上でデータを扱う"]
    C --> C2["列単位でデータを扱う"]

    M2 --> Fast["効率的な集計・分析"]
    C2 --> Fast
```

覚え方：

```text
インメモリ
= どこで扱う？
→ RAM

列指向
= どの単位で扱う？
→ 列
```

---

# 11. Direct Lake

Microsoft Fabric では、OneLake にデータが格納されている場合、Direct Lake を利用できる。

```mermaid
flowchart TD
    O["OneLake"]
    D["Delta Table"]
    DL["Direct Lake"]
    SM["セマンティックモデルのメモリ"]
    A["集計・分析"]
    P["Power BI レポート"]

    O --> D
    D --> DL
    DL --> SM
    SM --> A
    A --> P
```

## Import モードとのイメージの違い

### Import

```mermaid
flowchart LR
    A["データソース"] -->|Import| B["モデル用データ"]
    B --> C["セマンティックモデル"]
    C --> D["メモリ"]
    D --> E["分析"]
```

### Direct Lake

```mermaid
flowchart LR
    A["OneLake<br/>Delta Table"] -->|Direct Lake| B["セマンティックモデルのメモリ"]
    B --> C["分析"]
```

重要なのは、

> **「コピーしない」= メモリへ読み込まない**

ではないということ。

Direct Lake でも分析のためにデータをメモリへ読み込む。

ポイントは、

> **分析用としてモデルに別途データをコピーするのではなく、OneLake 上の Delta テーブルからセマンティックモデルのメモリへ直接読み込む**

こと。

### 名前から覚える

```text
Direct + Lake

= Lake にあるデータを直接利用する
```

---

# 12. データ視覚化

セマンティックモデルを作成した後、そのモデルを使用してデータを視覚化し、Power BI レポートを作成する。

重要なのは、

> **「何を伝えたいか」に応じて適切な視覚化を選択すること。**

---

# 13. テーブル

関連する多数の値を表示する場合に適している。

```text
商品       売上
-------------------
Surface    500万円
Xbox       300万円
Office     200万円
```

> **多数の正確な値を確認 → テーブル**

---

# 14. カード

重要な数値やメトリックを個別に表示する。

```text
┌──────────────┐
│    総売上     │
│              │
│   1,000万円   │
└──────────────┘
```

> **重要な1つの数値 → カード**

---

# 15. 横棒グラフ・縦棒グラフ

個別のカテゴリの数値を比較する。

```text
Surface  ██████████  500万円
Xbox     ██████      300万円
Office   ████        200万円
```

> **カテゴリ間の比較 → 棒グラフ**

---

# 16. 折れ線グラフ

特に、時間に伴う傾向を見る場合に適している。

```text
売上
 ↑
 │              ●
 │          ●
 │      ●
 │  ●
 └────────────────→ 月
   1月  2月  3月  4月
```

> **時系列・傾向 → 折れ線グラフ**

---

# 17. 円グラフ

全体に占める割合を比較する。

```text
総売上 = 100%

Surface → 50%
Xbox    → 30%
Office  → 20%
```

> **全体に対する割合 → 円グラフ**

---

# 18. 散布図

2つの数値的な大きさの関係・相関を確認する。

例えば、

```text
広告費
  と
売上金額
```

の関係。

```text
売上
 ↑
 │             ●
 │        ●
 │      ●   ●
 │   ●
 │ ●
 └────────────────→ 広告費
```

> **2つの数値の関係・相関 → 散布図**

---

# 19. マップ

地域・場所による値の違いを視覚化する。

例：

- 地域別売上
- 都市別顧客数

> **地理的な違い → マップ**

---

# 20. 視覚化の選び方

視覚化の使い分けを図にすると次のようになる。

```mermaid
flowchart TD
    Q["何を確認したい？"]

    Q --> A["多数の正確な値"]
    A --> A2["テーブル"]

    Q --> B["重要な1つの数値"]
    B --> B2["カード"]

    Q --> C["カテゴリ同士の比較"]
    C --> C2["棒グラフ"]

    Q --> D["時間による傾向"]
    D --> D2["折れ線グラフ"]

    Q --> E["全体に占める割合"]
    E --> E2["円グラフ"]

    Q --> F["2つの数値の関係"]
    F --> F2["散布図"]

    Q --> G["地域・場所による違い"]
    G --> G2["マップ"]
```

この対応は試験対策として重要。

---

# 21. Power BI の対話型レポート

Power BI のレポートでは、関連する複数のビジュアルが連動する。

例えば「福岡」を選択した場合、

```mermaid
flowchart TD
    A["都市別売上"]
    B["福岡を選択"]

    A --> B

    B --> C["商品別売上"]
    B --> D["月別売上推移"]
    B --> E["その他の関連ビジュアル"]

    C --> C2["福岡のデータを反映"]
    D --> D2["福岡のデータを反映"]
    E --> E2["福岡のデータを反映"]
```

つまり、

> **1つのビジュアルを操作すると、関連する他のビジュアルも連動する。**

---

# 22. Power BI の AI を利用した視覚化

## スマートなストーリー

ビジュアルが示している内容について、文章による概要を自動生成する。

```text
データ
 ↓
グラフ
 ↓
AI
 ↓
文章による説明
```

---

## Copilot

レポートの内容について自然言語で質問したり、概要やビジュアルを取得したりできる。

```text
「このレポートをまとめて」
        ↓
     Copilot
        ↓
レポート内容について回答
```

---

## 主要なインフルエンサー

特定のメトリックへ強く影響している要因を識別する。

```text
「なぜ売上が高くなっている？」
              ↓
       影響要因を分析
```

> **「この結果に何が影響している？」を調べる機能**

---

## 分解ツリー

複数のディメンションにわたってドリルダウンし、値の原因を調べる。

```mermaid
flowchart LR
    Sales["売上"]
    Area["地域"]
    West["西日本"]
    City["都市"]
    Fukuoka["福岡"]
    Product["商品"]
    Surface["Surface"]

    Sales --> Area
    Area --> West
    West --> City
    City --> Fukuoka
    Fukuoka --> Product
    Product --> Surface
```

つまり、

> **複数の切り口で「どこが原因なのか？」を掘り下げる。**

---

# 23. DP-900 試験向け最重要ポイント

## 用語の対応

- **Measure**
  - 分析対象となる数値
  - 「何を分析する？」
  - 例：売上金額、販売数量

- **Dimension**
  - 分析するときの切り口
  - 「何別に分析する？」
  - 例：商品、顧客、時間

- **Fact Table**
  - 分析対象となるイベント・数値を保持する

- **Dimension Table**
  - 分析の切り口となる情報を保持する

- **Star Schema**
  - Fact を中心に Dimension が関連する

- **Snowflake Schema**
  - Dimension がさらに別の詳細テーブルと関連する

- **Hierarchy**
  - 年 → 月 → 日などの階層構造

- **Drill Down**
  - 大きい単位 → 小さい単位

- **Drill Up**
  - 小さい単位 → 大きい単位

- **Semantic Model**
  - データの意味・関係などを定義した分析用モデル

- **VertiPaq**
  - Import モードのセマンティックモデルのデータを効率的なインメモリ列指向ストアに格納する

- **Direct Lake**
  - OneLake の Delta テーブルからセマンティックモデルのメモリへ直接読み込み、分析する

---

# 24. 最終的な理解

今回登場した用語は、バラバラに暗記するのではなく、一連のデータ分析の流れとして理解する。

```mermaid
flowchart TD
    A["元データ"]

    A --> B["セマンティックモデル<br/>データの意味・関係を定義"]

    B --> C["メジャー<br/>何を分析する？"]
    B --> D["ディメンション<br/>何別に分析する？"]

    C --> E["Fact / Dimension<br/>としてモデル化"]
    D --> E

    E --> F["Star Schema<br/>Snowflake Schema"]

    F --> G["Hierarchy"]
    G --> H["Drill Down / Drill Up"]

    H --> I["効率的に集計・分析"]

    I --> J["VertiPaq<br/>インメモリ + 列指向"]

    I --> K["Direct Lake<br/>OneLakeから直接利用"]

    J --> L["データ視覚化"]
    K --> L

    L --> M["Power BI レポート"]
```

## 最後に一言で整理

```text
Semantic Model
→ データをどういう意味・関係で分析するか

Measure
→ 何を分析するか

Dimension
→ どの切り口で分析するか

Fact / Dimension Table
→ それらをどのようにテーブルとして構成するか

Star / Snowflake
→ テーブルをどのような関係で構成するか

Hierarchy
→ どの粒度で分析するか

VertiPaq
→ インポートされたデータを効率的に集計・分析する

Direct Lake
→ OneLake 上のデータを直接利用して分析する

Visualization
→ 分析結果をどのように見せるか
```

この「役割の違い」を理解しておけば、DP-900 の問題で似たような用語が並んでも、

**「これはデータの意味の話か？」
「分析対象の話か？」
「分析の切り口の話か？」
「保存・処理方式の話か？」
「視覚化の話か？」**

という観点から切り分けて判断できる。
