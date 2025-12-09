# 🌐 プロキシ環境での設定

企業ネットワークなどプロキシ環境で本ガイドの手順を実施する場合の設定方法です。

> 💻 **対象環境**: 本ページは **Windows + PowerShell** 環境向けです。Mac / Linux の場合は `$env:HTTP_PROXY = "..."` を `export HTTP_PROXY="..."` に読み替えてください。

> ⚠️ **注意**: 本ページの手順は **現在未検証** です。あくまで参考情報としてご利用ください。
>
> 「この手順でうまくいった」「この部分が動かなかった」などのフィードバックがあれば、[Issue](https://github.com/aktsmm/vscode-copilot-starter/issues) でお知らせいただけると助かります！

---

## 📑 目次

- [VS Code 本体](#vs-code-本体)
- [CLI ツール（Azure CLI / Git / GitHub CLI）](#cli-ツールazure-cli--git--github-cli)
- [winget（Windows パッケージマネージャー）](#wingetwindows-パッケージマネージャー)
- [プロキシ除外（No Proxy）](#プロキシ除外no-proxy)
- [トラブルシューティング](#トラブルシューティング)

---

## VS Code 本体

VS Code はシステムのプロキシ設定を自動検出しますが、うまくいかない場合は明示的に設定できます。

`Ctrl + ,` で設定を開き、「proxy」で検索して以下を設定：

| 設定項目                 | 値の例                          | 説明                         |
| ------------------------ | ------------------------------- | ---------------------------- |
| `Http: Proxy`            | `http://proxy.example.com:8080` | プロキシサーバーの URL       |
| `Http: Proxy Strict SSL` | `false`                         | 自己署名証明書を許可する場合 |

または `settings.json` に直接記述：

```json
{
  "http.proxy": "http://proxy.example.com:8080",
  "http.proxyStrictSSL": false
}
```

> 💡 この設定は **VS Code 本体と拡張機能** に適用されます。ターミナル内の CLI ツールには別途設定が必要です。

📖 公式ドキュメント: [VS Code Network Settings](https://code.visualstudio.com/docs/setup/network)

---

## CLI ツール（Azure CLI / Git / GitHub CLI）

VS Code のターミナルは **シェル（PowerShell）を埋め込んでいるだけ** なので、CLI ツールには VS Code のプロキシ設定が適用されません。

> 💡 各 CLI ツールによってプロキシ設定の参照先が異なります。環境変数で一括設定するのが簡単ですが、うまくいかない場合は各ツールの公式ドキュメントを参照してください。

### 環境変数で一括設定（推奨）

PowerShell で以下を実行すると、ほとんどの CLI ツールがプロキシを認識します：

```powershell
# 現在のセッションのみ有効
$env:HTTP_PROXY = "http://proxy.example.com:8080"
$env:HTTPS_PROXY = "http://proxy.example.com:8080"
```

**永続化する場合**（ユーザー環境変数に追加）:

```powershell
[Environment]::SetEnvironmentVariable("HTTP_PROXY", "http://proxy.example.com:8080", "User")
[Environment]::SetEnvironmentVariable("HTTPS_PROXY", "http://proxy.example.com:8080", "User")
```

### 各ツール個別の設定

#### Git

```powershell
git config --global http.proxy http://proxy.example.com:8080
git config --global https.proxy http://proxy.example.com:8080

# 設定確認
git config --global --get http.proxy

# 設定解除
git config --global --unset http.proxy
```

#### Azure CLI

Azure CLI は環境変数 `HTTP_PROXY` / `HTTPS_PROXY` を参照します。

```powershell
# 確認（プロキシ経由でログインできれば OK）
az login
```

#### GitHub CLI

GitHub CLI も環境変数 `HTTP_PROXY` / `HTTPS_PROXY` を参照します。

```powershell
# 確認
gh auth status
```

---

## winget（Windows パッケージマネージャー）

CLI ツールのインストールに使う `winget` もプロキシ設定が必要な場合があります。

winget は **システムのプロキシ設定**（インターネットオプション）を使用します。

1. **Windows 設定** → **ネットワークとインターネット** → **プロキシ**
2. **手動プロキシ セットアップ** でプロキシを設定

または、環境変数を設定してから winget を実行：

```powershell
$env:HTTP_PROXY = "http://proxy.example.com:8080"
$env:HTTPS_PROXY = "http://proxy.example.com:8080"
winget install Git.Git
```

---

## プロキシ除外（No Proxy）

社内サーバーなど、プロキシを経由したくない接続先がある場合は `NO_PROXY` 環境変数を設定します。

```powershell
# 現在のセッションのみ有効
$env:NO_PROXY = "localhost,127.0.0.1,.local.example.com"
```

**永続化する場合**:

```powershell
[Environment]::SetEnvironmentVariable("NO_PROXY", "localhost,127.0.0.1,.local.example.com", "User")
```

> 💡 カンマ区切りで複数のホストを指定できます。`.example.com` のようにドットで始めると、そのドメイン配下すべてが除外されます。

---

## トラブルシューティング

### 証明書エラーが出る場合

企業のプロキシが SSL インスペクションを行っている場合、証明書エラーが発生することがあります。

```powershell
# Git: SSL 検証を無効化（セキュリティリスクがあるため一時的な回避策として）
git config --global http.sslVerify false

# Azure CLI: 証明書検証を無効化
$env:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = "1"
```

> ⚠️ **注意**: SSL 検証の無効化はセキュリティリスクがあります。IT 部門に相談して、適切な CA 証明書をインストールすることを推奨します。

### プロキシ認証が必要な場合

ユーザー名とパスワードが必要なプロキシの場合：

```powershell
$env:HTTP_PROXY = "http://username:password@proxy.example.com:8080"
$env:HTTPS_PROXY = "http://username:password@proxy.example.com:8080"
```

---

## 関連ドキュメント

- [VS Code Network Settings](https://code.visualstudio.com/docs/setup/network)
- [Git プロキシ設定](https://git-scm.com/docs/git-config#Documentation/git-config.txt-httpproxy)
- [Azure CLI プロキシ設定](https://learn.microsoft.com/ja-jp/cli/azure/use-cli-effectively#work-behind-a-proxy)
