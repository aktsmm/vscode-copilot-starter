# 🔐 Azure CLI の権限とロールについて

Azure CLI で Azure リソースを操作するには、適切な権限（RBAC ロール）が必要です。本ページでは、必要な権限と権限がない場合の対処法を説明します。

---

## 📑 目次

- [必要な権限レベル](#必要な権限レベル)
- [権限がない場合でもできること](#権限がない場合でもできること)
- [権限エラーの確認方法](#権限エラーの確認方法)
- [権限を取得する方法](#権限を取得する方法)
- [よくあるエラーと対処法](#よくあるエラーと対処法)

---

## 必要な権限レベル

### 操作別の必要ロール

| 操作                   | 必要な最小ロール                                     | 説明                         |
| ---------------------- | ---------------------------------------------------- | ---------------------------- |
| リソースの閲覧         | **閲覧者 (Reader)**                                  | リソースの一覧表示、詳細確認 |
| リソースの作成・変更   | **共同作成者 (Contributor)**                         | リソースの作成、更新、削除   |
| ロールの割り当て       | **所有者 (Owner)** または **ユーザーアクセス管理者** | 他ユーザーへの権限付与       |
| サブスクリプション管理 | **所有者 (Owner)**                                   | サブスクリプション設定の変更 |

### Bicep デプロイに必要な権限

Bicep テンプレートをデプロイするには、**デプロイ先のスコープ**に応じた権限が必要です。

| デプロイスコープ   | コマンド例                   | 必要な権限                           |
| ------------------ | ---------------------------- | ------------------------------------ |
| リソースグループ   | `az deployment group create` | リソースグループに対する共同作成者   |
| サブスクリプション | `az deployment sub create`   | サブスクリプションに対する共同作成者 |
| 管理グループ       | `az deployment mg create`    | 管理グループに対する共同作成者       |

> 💡 **おすすめ**: 可能であれば **サブスクリプション スコープで共同作成者 (Contributor)** があると便利です。リソースグループの作成からリソースのデプロイまで、ほとんどの操作がカバーできます。

---

## 権限がない場合でもできること

**Azure への書き込み権限がなくても、以下の作業は可能です：**

| 作業                                 | 説明                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| ✅ Bicep ファイルの編集              | VS Code + Bicep 拡張機能でシンタックスハイライト、補完が動作 |
| ✅ Bicep の構文チェック              | `az bicep build -f main.bicep` でコンパイルエラーを確認      |
| ✅ ARM テンプレートへの変換          | `az bicep build -f main.bicep` で JSON 出力                  |
| ✅ What-If（権限があるスコープのみ） | `az deployment group what-if` で変更内容をプレビュー         |
| ✅ Copilot によるコード補完          | Bicep コードの提案・説明・レビュー                           |

> 💡 **ヒント**: 権限がなくても Bicep の学習・開発は十分に可能です。実際のデプロイ時のみ権限が必要になります。

---

## 権限エラーの確認方法

### 現在のユーザー情報を確認

```powershell
# ログイン中のアカウント情報
az account show --query "{Name:name, User:user.name, SubscriptionId:id}" -o table

# 現在のサブスクリプションでの自分のロール
az role assignment list --assignee $(az ad signed-in-user show --query id -o tsv) --query "[].{Role:roleDefinitionName, Scope:scope}" -o table
```

### サブスクリプション一覧と権限

```powershell
# アクセス可能なサブスクリプション一覧
az account list --query "[].{Name:name, State:state, IsDefault:isDefault}" -o table

# 特定のサブスクリプションに切り替え
az account set --subscription "サブスクリプション名またはID"
```

---

## 権限を取得する方法

### 1. 管理者にロールを依頼

Azure の権限は **サブスクリプションの所有者** または **ユーザーアクセス管理者** が付与できます。

**依頼時に伝える情報:**

```
■ 権限依頼
・対象者: [自分のメールアドレス / UPN]
・対象スコープ: [サブスクリプション名 または リソースグループ名]
・必要なロール: 共同作成者 (Contributor)
・理由: [Bicep テンプレートのデプロイのため 等]
```

### 2. 自分で確認できる場合（Azure ポータル）

1. [Azure ポータル](https://portal.azure.com) にログイン
2. **サブスクリプション** → 対象のサブスクリプションを選択
3. **アクセス制御 (IAM)** → **自分のアクセス権の表示**
4. 現在のロールを確認

### 3. 開発・テスト用の無料サブスクリプション

自分専用の環境が必要な場合：

| オプション                                                                                                                        | 説明                                             |
| --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| [Azure 無料アカウント](https://azure.microsoft.com/ja-jp/free/)                                                                   | 12 ヶ月無料 + $200 クレジット（30 日間）         |
| [Visual Studio サブスクリプション](https://azure.microsoft.com/ja-jp/pricing/member-offers/credit-for-visual-studio-subscribers/) | VS サブスクライバー向け月額クレジット            |
| [Azure for Students](https://azure.microsoft.com/ja-jp/free/students/)                                                            | 学生向け $100 クレジット（クレジットカード不要） |

---

## よくあるエラーと対処法

### AuthorizationFailed

```
The client 'xxx@example.com' with object id 'xxx' does not have authorization
to perform action 'Microsoft.Resources/deployments/write' over scope '/subscriptions/xxx'
```

**原因**: デプロイに必要な権限がない

**対処法**:

1. 管理者に共同作成者ロールを依頼
2. 権限のある別のサブスクリプション/リソースグループを使用
3. What-If (`--what-if`) で変更内容だけ確認する

### InvalidSubscriptionId / SubscriptionNotFound

```
The subscription 'xxx' could not be found.
```

**原因**: 指定したサブスクリプションにアクセス権がない、または存在しない

**対処法**:

```powershell
# アクセス可能なサブスクリプション一覧を確認
az account list -o table

# 正しいサブスクリプションに切り替え
az account set --subscription "正しいサブスクリプション名"
```

### 認証トークンの期限切れ

```
AADSTS700082: The refresh token has expired due to inactivity.
```

**対処法**:

```powershell
# 再ログイン
az login
```

### MFA（多要素認証）エラー

```
AADSTS50076: Due to a configuration change made by your administrator...
```

**対処法**:

```powershell
# デバイスコード認証を使用
az login --use-device-code
```

---

## 参考リンク

- [Azure RBAC ドキュメント](https://learn.microsoft.com/ja-jp/azure/role-based-access-control/overview)
- [Azure 組み込みロール一覧](https://learn.microsoft.com/ja-jp/azure/role-based-access-control/built-in-roles)
- [Azure CLI 認証](https://learn.microsoft.com/ja-jp/cli/azure/authenticate-azure-cli)
- [README に戻る](../README.MD)
