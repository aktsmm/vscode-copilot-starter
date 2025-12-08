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

### 方法 1: copilot-instructions.md に記載（推奨）

`.github/copilot-instructions.md` に以下のルールを追加：

```markdown
## MCP ツールの利用

- **Azure / Microsoft / Bicep 製品に関する質問には必ず `mcp_microsoftdocs` ツールを使用して MS Learn から情報を取得すること。**
- 回答には必ずソース URL を明記する。
- 対象: Azure, Bicep, ARM, .NET, Microsoft 365, Power Platform, GitHub (Microsoft 関連), VS Code 拡張機能 など。
```

### 方法 2: Chat Mode で MCP ツールを指定

`.github/copilot-chat-modes/azure.chatmode.md` を作成：

```markdown
---
name: "Azure Mode"
description: "Azure 開発に特化（MS Learn 参照必須）"
tools:
  - run_in_terminal
  - read_file
  - replace_string_in_file
  - mcp_microsoftdocs_microsoft_docs_search
  - mcp_microsoftdocs_microsoft_docs_fetch
  - mcp_microsoftdocs_microsoft_code_sample_search
---

# Azure Mode

Azure 関連の開発に特化したモードです。

## ルール

- **必ず MS Learn MCP サーバーを使用して最新ドキュメントを参照すること**
- 回答にはソース URL を含める
- Azure CLI / Bicep コードを優先

## MCP ツールの使い分け

| ツール | 用途 |
|--------|------|
| `microsoft_docs_search` | ドキュメント検索（概要把握） |
| `microsoft_docs_fetch` | 特定ページの詳細取得 |
| `microsoft_code_sample_search` | コードサンプル検索 |
```

**使い方**: Copilot Chat 画面上部のモードセレクタから「Azure Mode」を選択

### 方法 3: Agent に MCP ツールを組み込む

`.github/agents/azure-expert.agent.md` を作成：

```markdown
---
name: "Azure Expert"
description: "Azure の専門家（MS Learn 参照必須）"
tools:
  - run_in_terminal
  - read_file
  - replace_string_in_file
  - mcp_microsoftdocs_microsoft_docs_search
  - mcp_microsoftdocs_microsoft_docs_fetch
---

# Azure Expert Agent

あなたは Azure の専門家です。

## 必須ルール

1. **Azure 関連の質問には必ず `mcp_microsoftdocs` ツールで MS Learn を検索すること**
2. 回答には必ずソース URL を明記
3. 古い情報や推測で回答しない

## 検索の流れ

1. `microsoft_docs_search` で関連ドキュメントを検索
2. 必要に応じて `microsoft_docs_fetch` で詳細を取得
3. 検索結果を元に回答を構成
```

**使い方**: Copilot Chat で `@azure-expert` と入力

### 確認方法

MCP サーバーが正しく動作しているか確認：

1. Copilot Chat を開く
2. 「Bicep で App Service を作成する方法」と質問
3. 回答に `https://learn.microsoft.com/...` のリンクが含まれていれば OK

> 💡 **ヒント**: MCP ツールが使用されると、Copilot Chat の回答に「🔧 Used tools: microsoft_docs_search」のような表示が出ます。

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

```markdown
# Repository Copilot Instructions

## コミュニケーションスタイル

- 日本語で回答する
- 簡潔で実行可能な返信を優先

## 許可される操作

- ファイルの読み取り・編集
- ターミナルでのコマンド実行（`az`, `gh`, `git` など）

## 禁止される操作

- 機密情報のコミット
- `main` ブランチへの直接プッシュ
```

### Instructions（ドメイン指示）

特定のファイルパターンに適用されるルールです。Bicep ファイル専用のルールなどを定義できます。

**設定場所**: `.github/instructions/*.instructions.md`

**例**: `.github/instructions/bicep.instructions.md`

```markdown
---
applyTo: "**/*.bicep"
---

# Bicep ファイル用の指示

- モジュール分割を推奨（`modules/` ディレクトリ配下に配置）
- パラメータには `@description` デコレータで説明を付与
- シークレットは `@secure()` デコレータを使用
- `existing` キーワードで既存リソース参照
```

> 💡 `applyTo` で glob パターンを指定すると、そのパターンに一致するファイルを編集する際に自動適用されます。

### Prompts（プロンプトテンプレート）

繰り返し使うプロンプトをテンプレート化できます。

**設定場所**: `.github/prompts/*.prompt.md`

**例**: `.github/prompts/bicep-review.prompt.md`

```markdown
---
mode: "agent"
description: "Bicep ファイルをレビューする"
---

以下の観点で Bicep ファイルをレビューしてください：

1. **セキュリティ**: シークレットが適切に保護されているか
2. **命名規則**: リソース名が一貫しているか
3. **モジュール化**: 再利用可能な構造になっているか
4. **パラメータ**: 説明とデフォルト値が適切か

対象ファイル: ${file}
```

**使い方**: Copilot Chat で `/bicep-review` と入力（ファイル名から `.prompt.md` を除いた名前）

### Agents（カスタムエージェント）

特定のタスクに特化したエージェントを定義できます。

**設定場所**: `.github/agents/*.agent.md`

**例**: `.github/agents/bicep-expert.agent.md`

```markdown
---
name: "Bicep Expert"
description: "Azure Bicep の専門家エージェント"
tools:
  - run_in_terminal
  - read_file
  - replace_string_in_file
---

# Bicep Expert Agent

あなたは Azure Bicep の専門家です。以下のガイドラインに従ってください：

## 専門分野

- Bicep テンプレートの作成・レビュー
- ARM テンプレートから Bicep への変換
- Azure リソースのベストプラクティス

## 使用するコマンド

- `az bicep build` - 構文チェック
- `az deployment group what-if` - デプロイ前確認
- `az bicep decompile` - ARM から変換

## 回答スタイル

- コード例を必ず含める
- MS Learn のドキュメントを参照する
```

**使い方**: Copilot Chat で `@bicep-expert` と入力

### Chat Modes（チャットモード）

Copilot Chat の動作モードをカスタマイズできます。

**設定場所**: `.github/copilot-chat-modes/*.chatmode.md`

**例**: `.github/copilot-chat-modes/infra.chatmode.md`

```markdown
---
name: "Infrastructure Mode"
description: "インフラ構築に特化したモード"
tools:
  - run_in_terminal
  - read_file
  - mcp_microsoftdocs_microsoft_docs_search
---

# Infrastructure Mode

Azure インフラストラクチャの構築・管理に特化したモードです。

## 有効なツール

- ターミナル実行（Azure CLI）
- ファイル読み取り
- MS Learn ドキュメント検索

## 応答ガイドライン

- Bicep コードを優先
- `--what-if` でのデプロイ前確認を推奨
- セキュリティベストプラクティスを常に考慮
```

**使い方**: Copilot Chat のモードセレクタから選択

---

## 推奨 VS Code 設定

`.vscode/settings.json` に追加すると便利な設定：

```json
{
  // Bicep
  "bicep.enableOutputTimestamps": true,

  // エディタ
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",

  // ファイル
  "files.autoSave": "afterDelay",
  "files.autoSaveDelay": 1000,

  // ターミナル
  "terminal.integrated.defaultProfile.windows": "PowerShell",

  // Git
  "git.autofetch": true,
  "git.confirmSync": false,

  // Copilot（必要に応じて）
  "chat.tools.autoApprove": false
}
```

### Bicep 固有の設定

```json
{
  "[bicep]": {
    "editor.defaultFormatter": "ms-azuretools.vscode-bicep",
    "editor.tabSize": 2,
    "editor.insertSpaces": true
  }
}
```

---

## 関連ドキュメント

- [README - 事前準備](../README.MD#-事前準備必須)
- [GitHub Copilot ドキュメント](https://docs.github.com/ja/copilot)
- [VS Code Settings リファレンス](https://code.visualstudio.com/docs/getstarted/settings)
