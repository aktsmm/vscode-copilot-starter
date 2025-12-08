# samples フォルダ

GitHub Copilot カスタマイズ用のサンプルファイル集です。  
必要なファイルを適切な場所にコピーして使用してください。

---

## 📁 ファイル一覧

### MCP / 設定ファイル

| ファイル                     | コピー先                | 説明                          |
| ---------------------------- | ----------------------- | ----------------------------- |
| `mcp-workspace.json`         | `.vscode/mcp.json`      | MCP サーバー設定（Workspace） |
| `mcp-user-settings.json`     | User Settings (JSON)    | MCP サーバー設定（User）      |
| `recommended-settings.jsonc` | `.vscode/settings.json` | 推奨 VS Code 設定             |

### Copilot カスタマイズ

| ファイル                  | コピー先                          | 説明                                 |
| ------------------------- | --------------------------------- | ------------------------------------ |
| `copilot-instructions.md` | `.github/copilot-instructions.md` | リポジトリ全体のルール               |
| `bicep.instructions.md`   | `.github/instructions/`           | Bicep ファイル用ルール               |
| `bicep-review.prompt.md`  | `.github/prompts/`                | Bicep レビュー用プロンプト           |
| `bicep-expert.agent.md`   | `.github/agents/`                 | Bicep 専門家エージェント             |
| `azure-expert.agent.md`   | `.github/agents/`                 | Azure 専門家エージェント（MCP 必須） |
| `azure.chatmode.md`       | `.github/copilot-chat-modes/`     | Azure 開発モード（MCP 必須）         |
| `infra.chatmode.md`       | `.github/copilot-chat-modes/`     | インフラ構築モード                   |

> ⚠️ **注意**: `.chatmode.md` ファイルは将来的に `.agent.md` に統合される可能性があります。最新の VS Code / Copilot バージョンでの動作を確認してください。

---

## 使い方

1. 必要なファイルを選ぶ
2. 上記の「コピー先」にコピー
3. 内容を自分のプロジェクトに合わせてカスタマイズ

---

[← README に戻る](../README.MD)
