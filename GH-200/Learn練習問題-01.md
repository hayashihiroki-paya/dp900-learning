# GH-200 GitHub Actions 練習問題 振り返り

## 結果

- スコア: **87%**
- 正解: **26 / 30問**
- 不正解: **4 / 30問**
- Microsoft Learn の目安: **複数回の受験で77%以上**
- 現状は合格目安を上回っており、全体的な基礎理解はできている。

## 全体的な傾向

今回の結果を見ると、**GitHub Actions のワークフローを実際に構成・運用するための知識はかなり定着している**。

特に以下は安定して回答できている。

- workflow / job / step の関係
- イベントによるワークフローのトリガー
- `env`
- `secrets`
- `actions/checkout`
- `actions/upload-artifact`
- cache
- matrix
- reusable workflow
- GitHub-hosted runner / self-hosted runner
- `action.yml`
- Docker container action
- Azure デプロイ
- GitHub Advanced Security

一方、不正解だった問題には共通して、

> **GitHub Actions 周辺機能の「役割・分類・境界」の理解**

が問われている傾向がある。

YAML の書き方やワークフローの動作そのものよりも、
「この機能は何なのか」「どこまでを担当するのか」という整理を重点的に復習するとよさそう。

---

# 不正解だった問題

## 1. Dependabot の役割

### 自分の回答

> コードの脆弱性を検出して修正するためのツール

### 正解

> 依存関係を最新の状態に保つためのツール

### 原因

Dependabot とセキュリティ機能の役割が少し混ざっている。

Dependabot には脆弱性対応の機能もあるため完全に的外れではないが、試験ではまず

**「依存関係を管理・更新する仕組み」**

として捉える。

### 整理

```text
Dependabot
├─ Dependabot version updates
│  └─ 依存関係を最新バージョンへ更新
│
├─ Dependabot alerts
│  └─ 脆弱性のある依存関係を通知
│
└─ Dependabot security updates
   └─ 脆弱性を修正する依存関係更新PRを作成
```

### 覚え方

**Dependabot = dependency（依存関係）の管理**

「脆弱性検出だけのツール」と狭く覚えない。

---

## 2. GitHub Actions の用途

### 自分の回答

> ソフトウェア開発タスクを自動化する

### 正解

> これらの機能はすべて

### 原因

GitHub Actions の用途を狭く捉えすぎた。

GitHub Actions は CI/CD だけではなく、GitHub 上のさまざまなイベントを起点として処理を自動化できる。

### 整理

GitHub Actions は例えば以下を自動化できる。

- build
- test
- lint
- deployment
- Issue / PR 操作
- release
- package publishing
- GitHub API を利用した処理
- 定期実行
- リポジトリ管理

### 覚え方

**GitHub Actions = GitHub を中心とした汎用ワークフロー自動化基盤**

「CI/CDツール」とだけ覚えない。

---

## 3. GitHub Script とは何か

### 自分の回答

> JavaScript にコンパイルするプログラミング言語

### 正解

> GitHub Actions から GitHub API へのアクセスを可能にするワークフローアクション

### 原因

GitHub Script を「言語」として捉えてしまった。

正しくは `actions/github-script` という **GitHub Action**。

### イメージ

```yaml
- uses: actions/github-script@v7
  with:
    script: |
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: 'Hello!'
      })
```

整理すると、

```text
GitHub Actions
  ↓
actions/github-script
  ↓
JavaScriptを書く
  ↓
GitHub APIを呼び出す
  ↓
Issue / PR / Repositoryなどを操作
```

### 覚え方

**GitHub Script = GitHub API を JavaScript から簡単に操作するための Action**

GitHub Actions と GitHub Script の関係は、

```text
GitHub Actions
└─ workflow
   └─ job
      └─ step
         └─ actions/github-script
```

という位置付け。

---

## 4. GitHub Actions の3種類

### 自分の回答

> シーケンシャル アクション、並列アクション、複合アクション

### 正解

> コンテナー アクション、JavaScript アクション、複合アクション

### 原因

**「Action の実装方式」**と**「workflow/job の実行方式」**が混ざった。

### 正しい分類

| Action の種類 | イメージ |
|---|---|
| JavaScript Action | Node.js / JavaScript で実装 |
| Docker Container Action | Docker コンテナー内で実行 |
| Composite Action | 複数の step をまとめて Action 化 |

今回の別問題で、

> Ruby でカスタム GitHub Actionを作る → Docker Container Action

は正解できているため、個々の仕組みよりも**3種類をひとまとまりの分類として覚えられていなかった**と考えられる。

### 覚え方

```text
Custom Action
├─ JavaScript
├─ Docker
└─ Composite
```

---

# 弱点の共通パターン

4問をまとめると、

| 分野 | 状況 |
|---|---|
| workflow / job / step | ◎ |
| trigger / conditions | ◎ |
| matrix | ◎ |
| cache / artifact | ◎ |
| secrets / env | ◎ |
| runner | ◎ |
| Azure連携 | ◎ |
| `action.yml` | ◎ |
| Actionの実装 | ○ |
| GitHub周辺機能の役割 | △ |
| GitHub Script | △ |
| Dependabot | △ |

特に、

**「何ができるか」より「それが何者なのか」**

を問われた問題で間違えている。

例えば、

```text
Dependabot
→ 何者？
→ 依存関係管理

GitHub Script
→ 何者？
→ GitHub APIを操作するGitHub Action

JavaScript / Docker / Composite
→ 何の分類？
→ Custom Actionの実装方式

GitHub Actions
→ 何者？
→ GitHubイベントを起点に処理を自動化するワークフロー基盤
```

という形で整理するとよい。

---

# 今後の対策

## 優先度1: GitHub Actions 周辺機能を比較して整理する

以下を重点的に区別する。

```text
GitHub Actions
GitHub Script
Dependabot
GitHub Advanced Security
GitHub Packages
Secrets
Artifacts
Cache
Reusable workflows
Custom Actions
```

「それぞれが何を担当する機能なのか」を1～2文で説明できる状態を目指す。

---

## 優先度2: Custom Action の構造を整理する

今回、

- Docker Container Action
- `action.yml`

は正解できている。

そのため、以下を一つの体系として整理する。

```text
Custom Action
│
├─ JavaScript Action
│
├─ Docker Container Action
│
└─ Composite Action
        ↓
      action.yml
```

特に、

> Action の「種類」と workflow/job の「実行方法」は別物

と意識する。

---

## 優先度3: GitHub Script を実際に1回使う

知識だけで覚えるより、簡単な workflow を作る。

例えば、

```yaml
- uses: actions/github-script@v7
  with:
    script: |
      console.log(context.repo)
```

その後、

```text
context
github
github.rest
```

が何を表しているのか確認する。

これにより、

**GitHub Script = GitHub Actions の中から GitHub API を操作するための Action**

という位置付けが理解しやすくなる。

---

# 次回の練習問題で意識するポイント

問題文を見たら、

1. 「何の機能か」
2. 「何の分類を聞かれているか」
3. 「何ができるか」
4. 「どこまでを担当しているか」

を切り分ける。

特に、

> 「○○とは何ですか？」

という問題では、機能の一部ではなく**その機能の中心的な役割**を選ぶ。

---

# 今回のまとめ

今回の **87%** という結果から、GitHub Actions の基本的なワークフロー構築についてはかなり理解できている。

特にこれまで学習してきた、

```text
workflow
  ↓
job
  ↓
step
  ↓
action
```

という基本構造や、

```text
trigger
matrix
cache
artifact
runner
secrets
env
```

といった実際のワークフロー作成に関係する部分は安定している。

次の重点ポイントは、

> **GitHub Actions 周辺機能を「役割」と「分類」で整理すること**

特に以下の4点を復習する。

- Dependabot = **依存関係管理**
- GitHub Script = **GitHub API を操作するための Action**
- Custom Action = **JavaScript / Docker / Composite の3種類**
- GitHub Actions = **CI/CDに限定されない汎用ワークフロー自動化**

この部分を補強すれば、次回は **90%以上を安定して狙える状態**に近づけそう。
