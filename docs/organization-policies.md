# 🏢 組織ポリシーによる機能制限について

GitHub Copilot Business / Enterprise では、組織の管理者がポリシーで機能を制限できます。「機能が使えない」「モデルが選べない」場合は、組織ポリシーが原因の可能性があります。

> 💡 **対象**: 主に **Copilot Business / Enterprise** ユーザー向けの情報です。個人プラン（Free / Pro / Pro+）には該当しません。

---

## 📑 目次

- [制限される可能性がある機能](#制限される可能性がある機能)
- [確認方法](#確認方法)
- [制限を解除したい場合](#制限を解除したい場合)
- [よくある質問](#よくある質問)

---

## 制限される可能性がある機能

### 🔧 MCP サーバー

**症状**: VS Code の設定で `Chat > Mcp: Access` が「**organization によって管理されています**」と表示され、`none` に固定されている。

<img src="images/mcp-org-policy.png" width="70%">

**影響**: MCP サーバー（MS Learn Docs MCP など）が利用できない。

**確認方法**:

1. VS Code で `Ctrl + ,` → 設定を開く
2. 「mcp」で検索
3. `Chat > Mcp: Access` の値と「organization によって管理されています」の表示を確認

---

### 🤖 利用可能なモデル

**症状**: Chat のモデルピッカーで選択肢が少ない。

**影響**: Claude Opus、GPT-5.1、Gemini 3 など、本来プランで使えるはずのモデルが表示されない。

**確認方法**:

1. Copilot Chat を開く（`Ctrl + Shift + I`）
2. モデル選択ドロップダウンをクリック
3. 表示されるモデル数を確認

**プランで本来使えるモデル（参考）**:

| モデル                | Free | Pro | Business | Enterprise |
| --------------------- | :--: | :-: | :------: | :--------: |
| GPT-4.1               |  ✓   |  ✓  |    ✓     |     ✓      |
| GPT-5                 |  -   |  ✓  |    ✓     |     ✓      |
| GPT-5.1 / 5.2         |  -   |  ✓  |    ✓     |     ✓      |
| Claude Sonnet 4 / 4.5 |  -   |  ✓  |    ✓     |     ✓      |
| Claude Opus 4.5       |  -   |  ✓  |    ✓     |     ✓      |
| Claude Opus 4.1       |  -   |  -  |    -     |     ✓      |
| Gemini 2.5 Pro        |  -   |  ✓  |    ✓     |     ✓      |
| Gemini 3 Flash / Pro  |  -   |  ✓  |    ✓     |     ✓      |

> 📖 最新情報: [GitHub Docs - Plans for GitHub Copilot](https://docs.github.com/en/copilot/get-started/plans)

---

### 🚀 Preview 機能

**症状**: 新しいモデルや機能が使えない。

**影響**: Preview / Experimental 扱いの機能（新モデル、新機能など）が利用不可。

**理由**: 管理者が「Preview」ポリシーを無効にしている。

---

### 🤖 Coding Agent

**症状**: GitHub の Issue に Copilot を割り当てても動作しない。

**影響**: 自律的なコード生成・PR 作成ができない。

**理由**: 組織ポリシーで Coding Agent が無効化されている。

---

### 📝 その他の制限

| 機能                           | 制限の可能性                                       |
| ------------------------------ | -------------------------------------------------- |
| **コンテンツ除外**             | 特定ファイル/フォルダを Copilot の参照対象から除外 |
| **公開コードとの一致ブロック** | 公開リポジトリのコードと一致する提案をブロック     |
| **テレメトリ**                 | 使用状況データの収集を制限                         |
| **サードパーティ拡張機能**     | 特定の Copilot 拡張機能の利用を制限                |

---

## 確認方法

### VS Code での確認

1. **設定画面**: `Ctrl + ,` → 検索バーで機能名を検索
2. **「organization によって管理されています」** の表示があれば組織ポリシーで制御されている

### GitHub での確認（管理者向け）

1. GitHub.com にログイン
2. Organization Settings → **Copilot** → **Policies**
3. 各ポリシーの有効/無効を確認

---

## 制限を解除したい場合

### Step 1: 理由を確認

まず、なぜ制限されているか確認しましょう：

- **セキュリティ要件**: 機密データの外部送信を防ぐため
- **コンプライアンス**: 業界規制への対応
- **コスト管理**: Premium requests の消費を抑制
- **まだ評価中**: 新機能の安全性を確認中

### Step 2: IT 管理者に相談

以下の情報を整理して相談すると話がスムーズです：

```
■ 相談内容
・解除したい機能: [例: MCP サーバー / Claude Opus 4.5 / Preview 機能]
・理由: [例: MS Learn を参照して正確な回答を得たい]
・対象者: [例: 開発チーム 10 名]

■ 現在の状況
・エラーメッセージ: [あれば記載]
・設定画面のスクリーンショット: [添付]
```

### Step 3: 管理者が設定を変更

管理者は GitHub.com で以下の手順で設定を変更できます：

1. **Organization Settings** → **Copilot** → **Policies**
2. 該当するポリシーを変更
3. 変更が反映されるまで数分待つ

---

## よくある質問

### Q: 個人の設定で解除できますか？

**A: いいえ。** 組織ポリシーは個人設定より優先されます。管理者のみが変更できます。

### Q: 制限されていても課金されますか？

**A: はい。** ライセンス料は機能制限に関係なく発生します。

### Q: 別の組織アカウントを使えば制限を回避できますか？

**A: 技術的には可能ですが、組織のポリシーに違反する可能性があります。** 個人アカウントで Copilot Pro を契約すれば制限なく使えますが、業務利用は組織のルールに従ってください。

### Q: いつ制限が解除されますか？

**A: 組織によります。** セキュリティ評価が完了すれば解除される場合もあれば、恒久的に制限される場合もあります。

---

## 参考リンク

- [GitHub Docs - Managing policies for Copilot in your organization](https://docs.github.com/en/copilot/managing-copilot/managing-github-copilot-in-your-organization/managing-policies-for-copilot-in-your-organization)
- [GitHub Docs - Plans for GitHub Copilot](https://docs.github.com/en/copilot/get-started/plans)
- [GitHub Copilot プラン比較ガイド](copilot-plans.md)
- [README に戻る](../README.MD)
