
Skip to main content

Auto





VSCodeのCodex拡張機能のほうでgithubのmcpサーバー経由でIssueの情報などを取得できるようにしたいです
copilot chatのほうでは使えてます
何を設定したらいいか教えてください
おそらく状況としては、

✅ VS Code の GitHub Copilot Chat (Agent Mode) では GitHub MCP が使えている
❌ VS Code の Codex拡張機能 では Issue や PR を取得できない
という状態ですね。

Codex拡張は Copilot Chat の MCP 設定を自動で引き継がないことがあります。Codex側に GitHub MCP を別途設定する必要があります。
openai
+ 1

まず確認
Codex のターミナルで



codex mcp list
を実行してください。

ここに



github
が出てこなければ、CodexはGitHub MCPを認識していません。
openai

GitHub MCPをCodexに追加
GitHub CopilotのMCPサーバーを利用する場合は、Codexの設定ファイルに追加します。

~/.codex/config.toml
Windowsなら通常



C:\Users\<ユーザー名>\.codex\config.toml
Mac/Linuxなら



~/.codex/config.toml
です。
deepwiki

設定例：



[mcp_servers.github]
command = "mcp-proxy"
args = [
  "--transport",
  "streamablehttp",
  "-H",
  "Authorization",
  "Bearer YOUR_GITHUB_PAT",
  "https://api.githubcopilot.com/mcp/"
]
GitHub Personal Access Token(PAT) を使用します。
zenn

mcp-proxy のインストール
GitHub MCPはHTTP型なので、Codexから接続するために mcp-proxy を使うケースが多いです。
zenn

例：



npm install -g mcp-proxy
または



brew install mcp-proxy
環境に応じてインストールします。
zenn

設定確認
追加後



codex mcp list
で



github
が表示されることを確認します。
openai

さらにCodexチャットで



利用可能なMCPツールを表示して
や



GitHubのIssue一覧を取得して
を試します。

VS Code 側で MCP が見えているのに Codex で使えない場合
最近の VS Code は



.vscode/mcp.json
または



{
  "servers": {
  }
}
形式で MCP を管理しています。
toolarc
+ 1

一方で Codex は



~/.codex/config.toml
を参照します。
openai
+ 1

つまり

Copilot Chatで動いている = Codexでも使える

ではないことがあります。

もし Windows 環境なら、

Codex拡張のバージョン
codex mcp list の結果
Copilot側の .vscode/mcp.json の GitHub MCP設定
を貼ってくれれば、Codex用の config.toml をそのまま作れるところまで一緒に確認できます。





Favicon typeFavicon typeFavicon type
ソース

Codex のターミナル　が何かわからないです
なるほどです 👍

まず確認したいのですが、あなたが言っている Codex拡張機能 は VS Code の左側にある OpenAI Codex の拡張ですよね。

私が前回書いた



codex mcp list
は Codex CLI (コマンドライン版) を使っている場合の確認方法でした。

もし VS Code の拡張機能だけを使っていて、ターミナルで codex コマンドを入れたことがなければ、その説明は当てはまらない可能性があります。

今の状況を整理すると、

✅ Copilot Chat では GitHub MCP が使える

❓ Codex拡張では GitHub MCP が見えない

ということですよね。

その場合はまず VS Code の設定を見たいです。

確認してほしいこと
VS Codeで



Ctrl + Shift + P
↓



MCP: List Servers
を実行してください。

表示された一覧に



github
や



GitHub
のようなサーバーはありますか？

もう一つ確認方法があります。

プロジェクトフォルダに



.vscode/mcp.json
というファイルがあれば、その内容を貼ってください。

または



{
  "servers": {
  }
}
のような設定ファイルがあれば、その中身を見せてください。

実は最近の VS Code では、

Copilot Chat が使う MCP
Codex拡張が使う MCP
が必ずしも同じ設定を見ていないケースがあります。

なので 「Copilotでは動くけどCodexでは見えない」 のはよくある症状です。

いちばん早いのは、VS Code の「MCP: List Servers」の結果を教えてもらうことです。 そこを見れば、GitHub MCP が本当に VS Code に登録されているのか、それとも Copilot 専用なのかが分かります。






{
    "servers": {
        "github": {
            "type": "http",
            "url": "https://api.githubcopilot.com/mcp/"
        }
    }
}


原因が見えました。

貼っていただいた内容を見ると、



"url": "<a href="https://api.githubcopilot.com/mcp/" ...
になっています。

本来は URL だけである必要があります。



{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
ただし、もっと重要なのは別の点です。

あなたの設定は VS Code の MCP 設定としては正常 に見えます。

つまり、

Copilot Chat → GitHub MCP利用可能
MCPサーバー定義あり
URLも GitHub Copilot MCP
なので、設定自体が大きく間違っている可能性は低いです。GitHub Copilot の MCP サーバーは HTTP URL で設定できます。
visualstudio

次に確認してほしいのは、

Codexから MCP が見えているか

です。

Codexのチャットでそのまま



使えるツールを一覧表示してください
または



GitHub MCPで利用できるツールを表示してください
と聞いてみてください。

もし正常なら、

list_issues
get_issue
search_repositories
get_pull_request
のような GitHub 関連ツールが表示されます。

逆に、



MCPツールはありません
や



利用可能なツールが見つかりません
になる場合は、

Codex拡張が VS Code の MCP 設定を読み込めていない可能性があります。

その場合は

VS Code のバージョン
Codex拡張の名前（OpenAI Codex / ChatGPT Codex など）
Codexのチャットで「利用可能なツールを表示して」と聞いた結果
を教えてください。

特に GitHub MCP が Copilot Chat では使えるのに Codexだけ使えない ケースは、設定の問題というより Codex拡張がVS CodeのMCP統合に対応しているかどうか の問題であることが多いです。そこを確認すれば切り分けできます。





Favicon type
ソース

この会話で使えるツールは次のとおりです。
作業・ファイル
 — シェルコマンドの実行
 — 実行中のコマンドへの入力・出力確認
 — ファイルの編集
 — ローカル画像の表示
MCP・連携
 /  — MCP リソースの一覧
 — MCP リソースの読み取り
 — 接続中の文書セッションの確認・操作
 — ChatGPT Pets の作成・管理
 — プラグインの検索・権限確認・管理
 — ペアレンタルコントロール等の確認・設定
 — Web 検索・ページ閲覧
会話・時間
 /  — 時刻確認・待機
 — 確認事項の質問
 — サブエージェントの起動・連絡・状態確認
GitHub、Linear、Jira の Issue 取得用ツールは、候補プラグインとして見つかりましたが、現時点では接続されていません。

これはかなり有力な情報です。

結論から言うと、

GitHub MCPサーバー自体は VS Code に登録されていますが、Codex側には接続されていません。

あなたが貼ってくれた結果に、

GitHub、Linear、Jira の Issue 取得用ツールは、候補プラグインとして見つかりましたが、現時点では接続されていません。

とあります。

つまり現在のCodexは

✅ MCP設定ファイルは見つけている

✅ GitHub用プラグインの存在も認識している

❌ 実際の接続・認証が完了していない

という状態です。

次にCodexで以下を実行してください。



mcp のプラグイン一覧を表示してください
または



plugin_management でインストール済みプラグインを表示してください
あなたのツール一覧に



mcp__codex_apps__plugin_management_*
があるので、Codex自身がプラグイン状態を確認できるはずです。

さらに試すなら



GitHub プラグインを有効化してください
または



GitHub MCP の接続状態を確認してください
とCodexに直接依頼してください。

実は重要なのですが、

あなたの環境では



list_mcp_resources
read_mcp_resource
plugin_management
しか見えていません。

もしGitHub MCPが正常接続されていたら、通常は



github_list_issues
github_get_issue
github_search_repositories
のようなGitHub由来のリソースやツールが見えるはずです。

しかし現状の一覧には存在していません。

そのため問題は

mcp.json の記述ミス
ではなく

Codex側でGitHub MCPへの認可がされていない
GitHub Pluginが未インストール
GitHub Pluginが無効
のどれかだと思われます。

次はCodexにこの一文を投げてみてください。



plugin_management を使って利用可能なプラグインと有効状態を表示してください
その結果を貼ってもらえれば、「未インストール」なのか「無効」なのかまで切り分けできます。






 に、インストール済みプラグインだけを一覧表示する機能は見当たりませんでした。検索で確認できたプラグインはどちらも  で、インストール済みの一覧とは判断できません。
そのため、この方法ではインストール済みプラグインを確実に列挙できません。

ここまでの情報から、原因はかなり絞れています。

Codex自身が

GitHub、Linear、Jira の Issue 取得用ツールは、候補プラグインとして見つかりましたが、現時点では接続されていません。

と言っているので、

MCP設定ファイルは読めているが、GitHub MCPサーバーとの接続は確立していない状態です。

また、

installed: false

という結果が出ているので、Codex内のプラグイン管理では GitHub 関連プラグインがまだ有効化されていない可能性が高いです。

まず試してほしいのは、Codexチャットで次のように聞くことです。



plugin_management で github を検索してください
または



利用可能な GitHub プラグインの詳細を表示してください
さらに、



GitHub プラグインをインストールしてください
または



GitHub MCP プラグインを有効化してください
を試してください。

もう1つ気になる点があります。

あなたが貼った JSON は本来こうである必要があります。



{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
しかし貼り付けた内容には



"url": "<a href="https://api.githubcopilot.com/mcp/"
という HTML が混じっています。

これは貼り付け時の変換だと思いますが、念のため VS Code の実ファイルを開いて、



https://api.githubcopilot.com/mcp/
だけになっていることを確認してください。

それと確認したいのですが、

あなたが使っている Codex はどちらですか？

VS Code Marketplace の「OpenAI Codex」
ChatGPT Plus/Pro で使える Codex Agent
その他
これが分かると、「VS CodeのMCPを自動利用できるタイプなのか」「別途プラグインインストールが必要なタイプなのか」を判断できます。

特に今の症状を見る限り、GitHub MCPサーバーそのものよりも Codex側のプラグイン接続が未完了 の可能性が高そうです。






VSCodeのCodex – OpenAI’s coding agentです
なるほど、状況がはっきりしました。

あなたが使っているのは

Codex – OpenAI's coding agent

ですね。

その場合、実は GitHub Copilot Chat の MCP と Codex の MCP は別物 です。

いまの情報を整理すると、

✅ VS Code の MCP 設定 (mcp.json) は存在する
✅ Copilot Chat では GitHub MCP が利用できる
✅ Codex は MCP サーバーの存在を認識している
❌ GitHub Issue 用ツールが接続されていない
❌ installed: false と表示される
なので、

Codex が VS Code の GitHub MCP を自動利用できていない状態

です。

さらに決定的なのは、Codex が表示したツール一覧に GitHub ツールがありません。

見えていたのは



list_mcp_resources
read_mcp_resource
plugin_management
などだけでした。

もし GitHub MCP が正常接続されていたら、



github_get_issue
github_list_issues
github_get_pull_request
のような GitHub 系ツールが並ぶはずです。

次にやるべきことは 1 つです。

Codex に以下を依頼してください。



list_mcp_resources を実行してください
または



利用可能な MCP リソースを一覧表示してください
その結果を見たいです。

もし GitHub MCP が接続されていれば、



github://...
のようなリソースが出ます。

出なければ、

Codex は mcp.json を見つけているだけで、GitHub MCP サーバーへの接続には失敗している

と判断できます。

実は OpenAI Codex 拡張は比較的新しく、GitHub Copilot Chat の MCP との完全な互換性がないケースがあります。GitHub Copilot の MCP URL を VS Code が使えることと、Codex がそのサーバーへ接続できることは別問題です。
openai
+ 1

まずは



list_mcp_resources
の結果を貼ってください。

それを見れば、

GitHub MCP接続失敗
認証失敗
CodexがMCPを読み込んでいない
GitHub MCPは見えているがツール公開に失敗
のどれかまで切り分けできます。





Favicon typeFavicon type
ソース

利用可能な MCP リソースは 6 件です。すべて  サーバーから提供されています。
種類リソース概要
プラグイン
Pets
ChatGPT Work mode のペット管理
プラグイン
Plugin Management
プラグインの検索、接続、権限、依存関係の管理
スキル
Create pet
ペットの作成・検証・アップロード
スキル
Pets
ペットの一覧・確認・選択・ダウンロード・削除
スキル
Update pet
ペットの情報やスプライトシートの確認・更新
スキル
plugin-management
プラグインの検索・権限確認・接続管理
この一覧には、GitHub・Linear・Jira の Issue を取得するリソースは含まれていません。


これで原因はほぼ確定です。

あなたの OpenAI Codex 拡張は現在、



codex_apps
の内蔵MCPしか接続されていません。

表示されているのが

Pets
Plugin Management
plugin-management
だけで、



github
というMCPサーバーが存在しません。

つまり

VS Code 全体では GitHub MCP が設定されている

しかし Codex 拡張はその GitHub MCP を読めていない

という状態です。

なぜそう判断できるのか
あなたの mcp.json



{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
は GitHub Copilot 用の MCP 設定です。

一方で Codex に



list_mcp_resources
を実行させると、



codex_apps
しか出てきません。

もし GitHub MCP が認識されていれば、

少なくとも



github
というサーバー名か、



github://
系のリソースが表示されるはずです。

しかし出ていません。

一番可能性が高い原因
OpenAI Codex 拡張が



.vscode/mcp.json
をまだ自動利用していないか、

GitHub Copilot 用 MCP を利用対象にしていない。

つまり



Copilot Chat
↓
GitHub MCP利用可能
 
Codex
↓
別管理
になっている可能性が高いです。

確認してほしいこと
Codex に次を聞いてください。



plugin_managementで github を検索してください
または



plugin_managementで github issue を検索してください
検索結果に



GitHub
installed: false
が出るなら、

続けて



そのプラグインを接続してください
を実行します。

もっと簡単な確認
VS Codeの



Ctrl+Shift+P
↓



Codex: Open Settings
または



Codex
で始まる設定を開いて、

MCP や Plugins 関連の設定があるか見てください。

現状の情報からすると、

mcp.json の書き方はほぼ問題なく、GitHub MCPサーバー側の問題でもありません。

むしろ

OpenAI Codex 拡張が GitHub Copilot の MCP 構成を取り込んでいない

可能性が非常に高いです。

もし可能であれば、VS Code の

拡張機能 → Codex
バージョン番号
を教えてください。OpenAI Codex のどのバージョンか分かれば、GitHub MCP 対応状況まで調べて切り分けできます。






image.png
ここ設定して使えるようになりません？
はい、この画面なら可能性があります。

画像を見る限り、これは Codex独自のMCP設定画面 です。

ただし、GitHub Copilotで使っている



{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
をそのまま使うのではなく、Codex側に同じサーバーを登録する必要があります。

まず試すなら以下です。

名前


github
タイプ


ストリーミング可能な HTTP
(STDIOではありません)

URL


https://api.githubcopilot.com/mcp/
作業ディレクトリ
そのまま



~/code
でOK

保存できたら、

Codexを再起動
MCP一覧を確認
「GitHubのIssueを取得して」と依頼
を試します。

ただし気になる点があります。

GitHub Copilot MCP は認証が必要です。

VS CodeのCopilot Chatでは認証済みだから動いていますが、Codexが同じ認証情報を引き継げるかは別問題です。

もし保存後に



401
Unauthorized
や



Authentication required
が出るなら、

GitHub PAT や GitHub認証の設定が別途必要です。

まずこの画面で

タイプを「ストリーミング可能な HTTP」
URL を https://api.githubcopilot.com/mcp/
で登録してみてください。

登録後に表示されるエラーや接続状態のスクリーンショットがあれば、そこからさらに切り分けできます。今の段階では、それが一番有力な方法です。







Copilot にメッセージを送信する

Copilot is an AI and may make mistakes.



Skip to main content




codexでgithub mcpが使えるようになるには結局何をすればいいんですか
もし Codex（OpenAI Codex CLIやCodex環境）で GitHub MCP (Model Context Protocol) を使いたい という意味なら、結局やることは次の3つです。

① GitHub Personal Access Token (PAT) を作る
GitHubの設定画面からPATを発行します。

最低限よく使う権限例：

repo（プライベートリポジトリ操作）
read:org（Organization参照）
workflow（GitHub Actions操作する場合）
GitHub:

Settings
↓
Developer settings
↓
Personal access tokens
↓
Tokens (classic)
または Fine-grained PAT を作成。

② GitHub MCP Server をインストール
Node.js が入っている状態で、



npx @modelcontextprotocol/server-github
または MCPサーバーとして設定。

たとえば Claude Desktop や MCP対応クライアントなら



{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-github"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_xxxxx"
      }
    }
  }
}
のような設定になります。

③ Codex側に MCP を登録
Codex CLI の設定ファイル（~/.codex/config.toml など）に MCPサーバーを追加します。

例：



[mcp_servers.github]
command = "npx"
args = ["-y", "@modelcontextprotocol/server-github"]
 
[mcp_servers.github.env]
GITHUB_PERSONAL_ACCESS_TOKEN = "ghp_xxxxx"
その後 Codex を起動。

動作確認
Codexに

このGitHubアカウントのリポジトリ一覧を取得して
や

issue #12 の内容を読んで
と依頼して反応すれば成功です。

よくあるハマりポイント
Node.jsが古い



node -v
Node 20以上推奨。

PATの権限不足

例えば

Repository not found
が出る場合はPATにリポジトリアクセス権限がないことが多いです。

Codex自体がMCP未対応の環境

現在は、

Claude Desktop
Cursor
Windsurf
Cline
Roo Code
OpenAI Codex CLI
などは MCP を利用できますが、利用方法は少しずつ違います。

もしあなたが使っているのが

OpenAI Codex CLI
VS Code の Copilot Agent
Cursor の Agent
Claude Desktop
のどれかを教えてくれれば、その環境向けの設定手順を具体的に説明できます。






vscodeでcodex使っててgiuhub mcpを使えるようにする内容の記事探してください
VS Code で Codex を使っていて、GitHub MCP をつなぎたいなら、まずはこの記事が一番参考になります。

まず読むべき記事
VSCodeでGitHubのMCP設定してみた（Zenn）
VS Code 1.99以降の MCP 設定画面から GitHub MCP Server を追加する流れがスクリーンショット付きで説明されています。
zenn

VS Code の設定から MCPサーバーを追加して GitHub Copilot agent mode で利用してみる（Qiita）
VS Code の settings.json / MCP 設定方法が詳しく解説されています。GitHub Copilot Agent と MCP の連携確認もあります。
qiita

Codex向けならこちらも重要
CodexとGitHubのリモートMCPサーバを接続する（Zenn）
Codex の ~/.codex/config.toml に GitHub MCP を設定する具体例があります。PAT を使った設定例も掲載されています。
zenn

codexでgithub-mcp-serverを直接使う
GitHub公式MCPサーバーを Codex から直接利用する設定例があります。最近の Codex の MCP 設定を追うなら参考になります。
rcmdnk

あなたの場合に一番可能性が高いパターン
弘基さんが以前から

VS Code
Codex拡張
GitHub
MCP
を触っているなら、

実は「GitHub MCP Serverそのものが未登録」か「PAT未設定」 のどちらかで詰まっているケースがほとんどです。

Codex 側で



codex mcp list
または



/mcp
を実行して、

github
connected
になっていれば接続できています。
なっていなければ設定不足です。
qiita
+ 1

GitHub MCP を使う目的が

Issue を読ませたい
PR を作りたい
Repository を検索したい
Copilot Coding Agent を使いたい
のどれなのか教えてもらえれば、今の VS Code + Codex 環境向けに最短手順だけまとめます。





Favicon typeFavicon typeFavicon type
ソース



Copilot にメッセージを送信する

Copilot is an AI and may make mistakes.



Skip to main content




このページの 
 env:GITHUB_PAT_TOKEN 
 って有効なコマンドですか？
はい。ただし、PowerShellの環境変数参照構文であって、単独のコマンドではありません。

記事中の



$env:GITHUB_PAT_TOKEN
は現在の PowerShell セッションに設定されている環境変数 GITHUB_PAT_TOKEN の値を表示する構文です。

また、記事の



$ env : GITHUB_PAT_TOKEN = "github_pat_xxxxxxxxx"
や



$ env : GITHUB_PAT_TOKEN
という表記は、Qiitaの表示上でスペースが入ってしまっているように見えます。実際に PowerShell で入力する場合は次のように書きます。



# 環境変数を設定
$env:GITHUB_PAT_TOKEN = "github_pat_xxxxxxxxx"
 
# 環境変数の値を確認
$env:GITHUB_PAT_TOKEN
記事でも「$env: による設定は現在の PowerShell セッションだけで有効」と説明されています。

もし GitHub MCP Server 用に設定しているのであれば、



$env:GITHUB_PAT_TOKEN = "あなたのPAT"
を実行した後、



$env:GITHUB_PAT_TOKEN
でトークン文字列が表示されれば正常です。

なお、あなたの開いている GitHub PAT 設定画面を見ると、今まさにこの手順を進めているようなので、PowerShell で実行しているコマンドがあれば貼ってもらえれば正しいか確認できます。





ソース

image.png

画像を見る限り、



github: failed (0 tools)
となっているので、GitHub MCP Serverへの接続に失敗しています。

一方で、



codex_apps: connected (26 tools)
codex_tui: connected (12 tools)
は正常接続されています。

GitHub MCP Serverでよくある原因は次の3つです。

① PATトークンが設定されていない
PowerShellで確認:



$env:GITHUB_PAT_TOKEN
何も表示されなければ未設定です。

設定:



$env:GITHUB_PAT_TOKEN="ghp_xxxxx"
または



$env:GITHUB_PAT_TOKEN="github_pat_xxxxx"
② PATの権限不足
GitHub MCP Serverでは通常、

Repository access
Issues
Pull requests
Contents
あたりの権限が必要です。

Fine-grained PAT を作成した場合は対象リポジトリへのアクセス権も必要です。

③ MCP設定ファイルの環境変数名が違う
たとえば設定が



{
  "github": {
    "command": "docker",
    "args": ["run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN"]
  }
}
なのに、



$env:GITHUB_PAT_TOKEN
を設定していると失敗します。

GitHub MCP Serverの設定によっては、



GITHUB_TOKEN
や



GITHUB_PERSONAL_ACCESS_TOKEN
を要求するケースがあります。

Codexなら次を実行すると原因がかなり分かります。



/mcp verbose
または



/mcp
で GitHub の詳細エラーを表示してください。

そのエラーメッセージが分かれば、

PAT設定ミス
権限不足
MCP設定ファイル不備
Docker起動失敗
のどれかを特定できます。






{ "servers": { "github": { "type": "http", "url": "https://api.githubcopilot.com/mcp/" } } } 
その設定を見る限り、環境変数 GITHUB_PAT_TOKEN は使われていません。

設定内容は



{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
になっています。

これは GitHub公式MCP Serverをローカル実行する構成ではなく、GitHub CopilotのMCPエンドポイントへHTTP接続する構成です。

一方、Qiitaの記事で出てくる



$env:GITHUB_PAT_TOKEN
は、たとえばこういう設定向けです。



{
  "github": {
    "command": "docker",
    "args": ["run", "..."],
    "env": {
      "GITHUB_PAT_TOKEN": "..."
    }
  }
}
つまり、

記事 → ローカルで GitHub MCP Server を起動
あなたの設定 → GitHub Copilot MCP に HTTP 接続
で構成が違います。

気になるのは画像の



github: failed (0 tools)
です。

この場合は PAT変数よりも、

GitHubへのサインイン状態
Copilotライセンス
MCP設定ファイルの記述ミス
URLの記述ミス
の可能性が高いです。

実は、あなたが貼ったJSONには



"url": "<a href="https://api.githubcopilot.com/mcp/" ...
というHTMLタグが混ざっています。

正しい設定は



{
  "servers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    }
  }
}
です。

もし実際の設定ファイルにも ... が入っているなら、その時点でJSONとして不正なので接続できません。

まずは Codex で



/mcp verbose
を実行して、github のエラー全文を確認してください。エラー内容が分かれば、認証エラーなのか設定エラーなのかを切り分けできます。






codex mcp add github --url https://api.githubcopilot.com/mcp/ --bearer-token-env-var GITHUB_PAT_TOKEN じゃあこのコマンドをトークンの環境変数名指定じゃなくてURLのエンドポイントだけで使えたりしません？ 
はい、できます。

むしろ、そのエンドポイントが GitHub Copilot の認証をそのまま利用できる環境 なら、--bearer-token-env-var GITHUB_PAT_TOKEN は不要です。

つまり、



codex mcp add github --url https://api.githubcopilot.com/mcp/
だけで接続できる可能性があります。

ただし、https://api.githubcopilot.com/mcp/ は公開APIではなく認証が必要なエンドポイントなので、Codex CLI がどう認証するか次第です。

一般的には次の2パターンです。

パターン1: CodexがGitHubログイン情報を使う


codex login
などでGitHub認証済みなら



codex mcp add github --url https://api.githubcopilot.com/mcp/
だけで動くことがあります。

パターン2: Bearer Tokenが必要
その場合は



codex mcp add github \
  --url https://api.githubcopilot.com/mcp/ \
  --bearer-token-env-var GITHUB_PAT_TOKEN
のようにトークンを渡します。

今回気になるのは、Qiitaの記事がHTML変換されていて、



codex mcp add github --url <a href="https://api.githubcopilot.com/mcp/">
となっていますが、実際に入力するコマンドは



codex mcp add github --url https://api.githubcopilot.com/mcp/
です。

... は記事表示用のHTMLなのでコマンドには含めません。

まず確認したいのは、Codexで



codex mcp list
または



/mcp verbose
の出力です。

特に



401 Unauthorized
なのか



connection refused
なのか



missing bearer token
なのかで対処が変わります。

画像の状態からすると、個人的には GitHub MCP Server自体は見つかっているが認証で失敗している 可能性が高そうです。/mcp verbose の github セクションのエラー全文が分かればすぐ切り分けできます。






image.png

この画像だと原因がかなり絞れました。



github: authentication required (0 tools)
になっているので、



failed
↓
authentication required
に変わっています。

つまり、

✅ GitHub MCP エンドポイントには到達できている
✅ MCP設定自体は認識されている
❌ 認証情報が渡されていない

という状態です。

なので、

URLだけで使えたりしません？

については、今の結果を見る限り「使えていない」 が答えです。

もし URL だけで利用可能なら、



github: connected
になるはずです。

逆に



authentication required
と出ているので、サーバー側が



認証トークンをください
と返しています。

Qiitaの記事のコマンド



codex mcp add github \
  --url https://api.githubcopilot.com/mcp/ \
  --bearer-token-env-var GITHUB_PAT_TOKEN
にはちゃんと意味があります。

Codexは実行時に



GITHUB_PAT_TOKEN
という環境変数を読んで、



Authorization: Bearer xxxxx
をMCPサーバーへ送ります。

PowerShellなら例えば



$env:GITHUB_PAT_TOKEN="github_pat_xxxxxxxxxxxxxxxxx"
を設定してから



codex mcp remove github


codex mcp add github --url https://api.githubcopilot.com/mcp/ --bearer-token-env-var GITHUB_PAT_TOKEN
を実行します。

その後、



/mcp
で



github: connected
になれば成功です。

ちなみに気になるのは、あなたが最初に開いていた GitHub の画面が



Fine-grained Personal Access Tokens
だったことです。

GitHub MCP Serverでは通常、



github_pat_xxxxx
形式の Fine-grained PAT がそのまま利用できます。

まずは PowerShell で



echo $env:GITHUB_PAT_TOKEN
を実行して値が存在するか確認し、

その状態で



codex mcp get github
または



codex mcp list
の結果を見てみてください。設定内容に bearer-token-env-var: GITHUB_PAT_TOKEN が入っていなければ、認証情報が渡っていないのが原因です。






codex mcp add github --url https://api.githubcopilot.com/mcp/ Added global MCP server 'github'. Detected OAuth support. Starting OAuth flow… Error: Registration failed: Dynamic registration failed: Registration failed: Dynamic client registration not supported 
このエラーはむしろ重要なヒントです。



Detected OAuth support. Starting OAuth flow…
Error: Registration failed:
Dynamic registration failed:
Registration failed:
Dynamic client registration not supported
つまり、

Codex はこの MCP サーバーを OAuth 対応だと認識した
OAuth 認証を開始しようとした
サーバー側が「Dynamic Client Registration」をサポートしていなかった
OAuth 認証に失敗した
という流れです。

なので今の状況は



github: authentication required
と整合しています。

Qiitaの記事のコマンドを見ると、



codex mcp add github \
  --url https://api.githubcopilot.com/mcp/ \
  --bearer-token-env-var GITHUB_PAT_TOKEN
となっているため、OAuthではなく PATをBearerトークンとして渡す前提 の設定だと考えられます。

まず PowerShell で



$env:GITHUB_PAT_TOKEN="github_pat_xxxxxxxxx"
を設定してから、



codex mcp remove github


codex mcp add github --url https://api.githubcopilot.com/mcp/ --bearer-token-env-var GITHUB_PAT_TOKEN
を実行してみるのがよいと思います。

あと一点気になるのは、あなたが実行したコマンドです。



codex mcp add github --url https://api.githubcopilot.com/mcp/
だけだと Codex が OAuth を試みます。

一方、記事のコマンドは



--bearer-token-env-var GITHUB_PAT_TOKEN
が付いています。

そのため記事の手順どおりにやるなら、

先に環境変数を作る ↓ その環境変数名を指定して mcp add する

が正しい順序です。

もし /mcp verbose が実行できるなら、その出力を貼ってください。特に github サーバー部分に PAT が渡されているかどうか確認できます。






このページ翻訳してインストール方法を説明してください
このページの内容を要約して、CodexでGitHub MCP Serverを使う手順を日本語で説明します。

結論
あなたが今やろうとしている方法なら、



codex mcp add github --url https://api.githubcopilot.com/mcp/ --bearer-token-env-var GITHUB_PAT_TOKEN
が正しい手順です。--bearer-token-env-var は必須と書かれています。

事前準備
必要なものは次の2つです。

OpenAI Codex
GitHub Personal Access Token (PAT)
方法1（推奨）
GitHub公式のリモートMCPを使う
① GitHub PATを作成
GitHubのPAT作成画面でトークンを発行します。

推奨権限例:

repo
workflow
read:org
project
gist
必要最小限から始めることが推奨されています。

② 環境変数を設定
PowerShell:



$env:GITHUB_PAT_TOKEN="github_pat_xxxxxxxxxxxxxxxxx"
確認:



$env:GITHUB_PAT_TOKEN
トークン文字列が表示されればOKです。

③ GitHub MCPを追加


codex mcp add github --url https://api.githubcopilot.com/mcp/ --bearer-token-env-var GITHUB_PAT_TOKEN
これは

GITHUB_PAT_TOKEN に保存されているPATをBearer Tokenとして使う

という意味です。

④ Codexで確認
Codexを起動して



/mcp
を実行します。

成功すると



github: connected
になります。

あなたの現在の状況
先ほどの画像では



github: authentication required
でした。

これは

✅ GitHub MCPサーバーには接続できている

❌ 認証トークンが渡っていない

という状態です。

そして実際に



Detected OAuth support
...
Dynamic client registration not supported
が出ていたので、

CodexがOAuth認証を試したが失敗した状態です。

このページの説明では、リモートMCPを使う場合は



--bearer-token-env-var GITHUB_PAT_TOKEN
を付ける方法になっています。

まずやるべきこと
PowerShellで



$env:GITHUB_PAT_TOKEN="あなたのPAT"
を実行してから、



codex mcp remove github


codex mcp add github --url https://api.githubcopilot.com/mcp/ --bearer-token-env-var GITHUB_PAT_TOKEN
を実行してください。

その後



/ mcp
で



github: connected
になれば成功です。

もし次に表示されるエラーがあれば、その全文を貼ってください。今の状態ならあと一歩で接続できそうです。





ソース



Copilot にメッセージを送信する

Copilot is an AI and may make mistakes.
