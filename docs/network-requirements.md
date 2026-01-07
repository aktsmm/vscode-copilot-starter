# 🔒 ファイアウォール・プロキシ環境での通信要件

本ガイドの各ツールを利用するために必要なネットワークエンドポイントをまとめています。

企業ネットワークなどでファイアウォールやプロキシサーバーが導入されている場合、以下のエンドポイントを許可リストに追加する必要があります。

> ⚠️ **注意**: 本ページの情報は **2026 年 1 月時点** のものです。最新情報は各公式ドキュメントを参照してください。

---

## 📑 目次

- [Azure CLI](#azure-cli)
- [GitHub Copilot](#github-copilot)
- [GitHub（Git 操作）](#githubgit-操作)
- [VS Code・拡張機能](#vs-code拡張機能)
- [参考リンク](#参考リンク)

---

## Azure CLI

Azure CLI を利用するために必要なエンドポイントです。`az login` や各種リソース操作に必要です。

### 必須エンドポイント（パブリック クラウド）

| 用途                   | エンドポイント                         |
| ---------------------- | -------------------------------------- |
| 認証（ログイン）       | `https://login.microsoftonline.com/`   |
| Azure Resource Manager | `https://management.azure.com/`        |
| 管理                   | `https://management.core.windows.net/` |
| Azure ポータル         | `https://portal.azure.com/`            |
| Microsoft Graph        | `https://graph.microsoft.com/`         |

### エンドポイント サフィックス

| 用途               | サフィックス                                                   |
| ------------------ | -------------------------------------------------------------- |
| ストレージ         | `*.core.windows.net`                                           |
| Key Vault          | `*.vault.azure.net`                                            |
| SQL Database       | `*.database.windows.net`                                       |
| Container Registry | `*.azurecr.io`                                                 |
| MySQL / PostgreSQL | `*.mysql.database.azure.com` / `*.postgres.database.azure.com` |

### 拡張機能のインストール

| 用途                 | エンドポイント                                |
| -------------------- | --------------------------------------------- |
| 拡張機能一覧取得     | `azcliextensionsync.blob.core.windows.net`    |
| 拡張機能ダウンロード | `azcliprod.blob.core.windows.net`             |
| 動的拡張機能推奨     | `azurecliextensionsync.blob.core.windows.net` |

> 📖 **公式ドキュメント**: [Azure CLI エンドポイント - MS Learn](https://learn.microsoft.com/ja-jp/cli/azure/azure-cli-endpoints)

---

## GitHub Copilot

GitHub Copilot を利用するために必要なエンドポイントです。

### 必須エンドポイント

| 用途                 | エンドポイント                                              |
| -------------------- | ----------------------------------------------------------- |
| 認証                 | `https://github.com/login/*`                                |
| ユーザー管理         | `https://api.github.com/user`                               |
| Copilot API          | `https://api.github.com/copilot_internal/*`                 |
| Copilot 提案サービス | `https://copilot-proxy.githubusercontent.com`               |
| Copilot 提案サービス | `https://origin-tracker.githubusercontent.com`              |
| Copilot 提案サービス | `https://*.githubcopilot.com/*`                             |
| テレメトリ           | `https://copilot-telemetry.githubusercontent.com/telemetry` |
| 分析                 | `https://collector.github.com/*`                            |
| テレメトリ           | `https://default.exp-tas.com`                               |

### プラン別エンドポイント

| プラン     | エンドポイント                           |
| ---------- | ---------------------------------------- |
| Individual | `https://*.individual.githubcopilot.com` |
| Business   | `https://*.business.githubcopilot.com`   |
| Enterprise | `https://*.enterprise.githubcopilot.com` |

> 💡 **ヒント**: 組織でサブスクリプションベースのネットワークルーティングを使用している場合、プラン別エンドポイントで特定プランのみ許可できます。

> 📖 **公式ドキュメント**: [Copilot allowlist reference - GitHub Docs](https://docs.github.com/en/copilot/managing-copilot/managing-github-copilot-in-your-organization/configuring-your-proxy-server-or-firewall-for-copilot)

---

## GitHub（Git 操作）

Git クローン、プッシュ、プルなどの操作に必要なエンドポイントです。

### 必須エンドポイント

| 用途               | エンドポイント                      |
| ------------------ | ----------------------------------- |
| GitHub API         | `https://api.github.com`            |
| Git 操作（HTTPS）  | `https://github.com`                |
| Git LFS            | `https://lfs.github.com`            |
| コンテンツ取得     | `https://*.githubusercontent.com`   |
| Raw ファイル       | `https://raw.githubusercontent.com` |
| コードダウンロード | `https://codeload.github.com`       |

### ポート

| プロトコル     | ポート |
| -------------- | ------ |
| HTTPS          | 443    |
| SSH（Git SSH） | 22     |

---

## VS Code・拡張機能

VS Code 本体と拡張機能のインストール・更新に必要なエンドポイントです。

### 必須エンドポイント

| 用途                       | エンドポイント                         |
| -------------------------- | -------------------------------------- |
| VS Code マーケットプレイス | `https://marketplace.visualstudio.com` |
| 拡張機能ダウンロード       | `https://*.vo.msecnd.net`              |
| VS Code 更新               | `https://update.code.visualstudio.com` |
| VS Code ダウンロード       | `https://code.visualstudio.com`        |
| Azure サービス             | `https://*.azure.com`                  |
| テレメトリ                 | `https://dc.services.visualstudio.com` |

> 📖 **公式ドキュメント**: [Network Connections in Visual Studio Code](https://code.visualstudio.com/docs/setup/network)

---

## 参考リンク

| ツール         | 公式ドキュメント                                                                                                                                                    |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Azure CLI      | [Azure CLI エンドポイント](https://learn.microsoft.com/ja-jp/cli/azure/azure-cli-endpoints)                                                                         |
| GitHub Copilot | [Copilot allowlist reference](https://docs.github.com/en/copilot/reference/copilot-allowlist-reference)                                                             |
| VS Code        | [Network Connections in Visual Studio Code](https://code.visualstudio.com/docs/setup/network)                                                                       |
| Visual Studio  | [Install behind a firewall or proxy server](https://learn.microsoft.com/en-us/visualstudio/install/install-and-use-visual-studio-behind-a-firewall-or-proxy-server) |
| Azure Portal   | [Azure portal URLs on your firewall](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-safelist-urls)                                               |

---

## 関連ページ

- [プロキシ環境での設定](proxy-settings.md) - プロキシサーバーの設定方法
- [README に戻る](../README.MD)
