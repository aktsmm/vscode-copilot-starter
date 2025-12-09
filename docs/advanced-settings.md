# 🔧 エンハンス設定（上級者向け）

環境構築後、さらに便利に使うための追加設定です。

---

## 📑 目次

- [Copilot ツールの自動承認](#copilot-ツールの自動承認)
- [MCP サーバーを強制的に使用させる](#mcp-サーバーを強制的に使用させる)
- [Copilot カスタマイズガイド](#copilot-カスタマイズガイド)
  - [ディレクトリ構造](#ディレクトリ構造)
  - [copilot-instructions.md（リポジトリ全体のルール）](#copilot-instructionsmdリポジトリ全体のルール)
  - [Instructions（ドメイン指示）](#instructionsドメイン指示)
  - [Prompts（プロンプトテンプレート）](#promptsプロンプトテンプレート)
  - [Agents（カスタムエージェント）](#agentsカスタムエージェント)
  - [Chat Modes（チャットモード）](#chat-modesチャットモード)
- [推奨 VS Code 設定](#推奨-vs-code-設定)

---

## Copilot ツールの自動承認

Copilot Chat がファイル編集やターミナル実行を行う際、毎回確認ダイアログが表示されます。  
信頼できる環境では自動承認を有効にすると作業効率が上がります。

### 全ツール自動承認（⚠️ 注意して使用）

**User Settings（全ワークスペース共通）**

`Ctrl + Shift + P` → `Preferences: Open User Settings (JSON)` で以下を追加：

```json
{
  "chat.tools.autoApprove": true
}
```

**Workspace Settings（このプロジェクトのみ）**

`.vscode/settings.json` に追加：

```json
{
  "chat.tools.autoApprove": true
}
```

### 特定ツールのみ自動承認（推奨）

読み取り系のみ自動承認し、書き込み系は確認する設定：

```json
{
  "chat.tools.autoApprove": [
    "read_file",
    "list_dir",
    "grep_search",
    "semantic_search",
    "file_search"
  ]
}
```

> ⚠️ **注意**: `run_in_terminal` や `replace_string_in_file` を自動承認すると、意図しない変更が行われる可能性があります。本番環境への影響があるコマンドは自動承認しないのが安全です。

---

## MCP サーバーを強制的に使用させる

Copilot Chat が Azure や Microsoft 関連の質問に対して、必ず MS Learn MCP サーバーを参照するように設定できます。

> 💡 **推奨**: 方法 1 の `copilot-instructions.md` による設定が最もシンプルで効果的です。リポジトリ全体に適用され、チームメンバー全員が同じルールで動作します。

### 方法 1: copilot-instructions.md に記載 ⭐ 推奨（ワークスペース単位）

`.github/copilot-instructions.md` に以下のルールを追加：

```markdown
## MCP ツールの利用

- **Azure / Microsoft / Bicep 製品に関する質問には必ず `mcp_microsoftdocs` ツールを使用して MS Learn から情報を取得すること。**
- 回答には必ずソース URL を明記する。
- 対象: Azure, Bicep, ARM, .NET, Microsoft 365, Power Platform, GitHub (Microsoft 関連), VS Code 拡張機能 など。
```

### 方法 1b: User Settings に記載（グローバル・全ワークスペース共通）

すべてのワークスペースで MCP サーバーを強制的に使用させたい場合は、User Settings に設定します。

1. `Ctrl + Shift + P` → `Preferences: Open User Settings (JSON)` を開く
2. 以下を追加：

```json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "Azure / Microsoft / Bicep 製品に関する質問には必ず mcp_microsoftdocs ツールを使用して MS Learn から情報を取得すること。回答には必ずソース URL を明記する。"
    }
  ]
}
```

> 💡 **使い分け**:
>
> - **方法 1（ワークスペース）**: チームで共有したいプロジェクト固有のルール → `.github/copilot-instructions.md`
> - **方法 1b（グローバル）**: 個人の全プロジェクトに適用したいルール → User Settings

### 方法 2: Chat Mode で MCP ツールを指定

`.github/copilot-chat-modes/azure.chatmode.md` を作成：

📄 [samples/azure.chatmode.md](../samples/azure.chatmode.md)

**使い方**: Copilot Chat 画面上部のモードセレクタから「Azure Mode」を選択

### 方法 3: Agent に MCP ツールを組み込む

`.github/agents/azure-expert.agent.md` を作成：

📄 [samples/azure-expert.agent.md](../samples/azure-expert.agent.md)

**使い方**: Copilot Chat で `@azure-expert` と入力

### 確認方法

MCP サーバーが正しく動作しているか確認：

1. Copilot Chat を開く
2. 「Bicep で App Service を作成する方法」と質問
3. 回答に `https://learn.microsoft.com/...` のリンクが含まれていれば OK

> 💡 **ヒント**: MCP ツールが使用されると、Copilot Chat の回答に「🔧 Used tools: microsoft_docs_search」のような表示が出ます。

### MCP ツール名の例

MS Learn MCP サーバーで使用される主なツール名：

| ツール名                                         | 用途                               |
| ------------------------------------------------ | ---------------------------------- |
| `mcp_microsoftdocs_microsoft_docs_search`        | MS Learn ドキュメントを検索        |
| `mcp_microsoftdocs_microsoft_docs_fetch`         | 特定の MS Learn ページの全文を取得 |
| `mcp_microsoftdocs_microsoft_code_sample_search` | コードサンプルを検索               |

> 💡 `copilot-instructions.md` でツール使用を強制する際は、`mcp_microsoftdocs` というプレフィックスで指定すると上記すべてのツールが対象になります。

---

## Copilot カスタマイズガイド

GitHub Copilot の動作をリポジトリ固有にカスタマイズできます。

### ディレクトリ構造

```
.github/
├── copilot-instructions.md      # リポジトリ全体のルール（必須）
├── agents/                       # カスタムエージェント定義
│   └── *.agent.md
├── copilot-chat-modes/           # チャットモード定義
│   └── *.chatmode.md
├── instructions/                 # ドメイン固有の指示
│   └── *.instructions.md
└── prompts/                      # 再利用可能なプロンプト
    └── *.prompt.md
```

### `copilot-instructions.md`（リポジトリ全体のルール）

すべての Copilot Chat セッションに適用される基本ルールです。

**設定場所**: `.github/copilot-instructions.md`

📄 [samples/copilot-instructions.md](../samples/copilot-instructions.md)

### Instructions（ドメイン指示）

特定のファイルパターンに適用されるルールです。Bicep ファイル専用のルールなどを定義できます。

**設定場所**: `.github/instructions/*.instructions.md`

**例**: `.github/instructions/bicep.instructions.md`

📄 [samples/bicep.instructions.md](../samples/bicep.instructions.md)

> 💡 `applyTo` で glob パターンを指定すると、そのパターンに一致するファイルを編集する際に自動適用されます。

### Prompts（プロンプトテンプレート）

繰り返し使うプロンプトをテンプレート化できます。

**設定場所**: `.github/prompts/*.prompt.md`

**例**: `.github/prompts/bicep-review.prompt.md`

📄 [samples/bicep-review.prompt.md](../samples/bicep-review.prompt.md)

**使い方**: Copilot Chat で `/bicep-review` と入力（ファイル名から `.prompt.md` を除いた名前）

### Agents（カスタムエージェント）

特定のタスクに特化したエージェントを定義できます。

**設定場所**: `.github/agents/*.agent.md`

**例**: `.github/agents/bicep-expert.agent.md`

📄 [samples/bicep-expert.agent.md](../samples/bicep-expert.agent.md)

**使い方**: Copilot Chat で `@bicep-expert` と入力

### Chat Modes（チャットモード）

> ⚠️ **注意**: Chat Modes は将来的に Agents に統合される可能性があります。新規作成は Agents（`.agent.md`）の使用を推奨します。

Copilot Chat の動作モードをカスタマイズできます。

**設定場所**: `.github/copilot-chat-modes/*.chatmode.md`

**例**: `.github/copilot-chat-modes/infra.chatmode.md`

📄 [samples/infra.chatmode.md](../samples/infra.chatmode.md)

**使い方**: Copilot Chat のモードセレクタから選択

---

## 推奨 VS Code 設定

`.vscode/settings.json` に追加すると便利な設定：

📄 [samples/recommended-settings.jsonc](../samples/recommended-settings.jsonc)

> 💡 このファイルをコピーして `.vscode/settings.json` に貼り付けてください（JSONC 形式なのでコメントは VS Code で有効です）。

---

## 関連ドキュメント

- [README - 事前準備](../README.MD#-事前準備必須)
- [GitHub Copilot ドキュメント](https://docs.github.com/ja/copilot)
- [VS Code Settings リファレンス](https://code.visualstudio.com/docs/getstarted/settings)
