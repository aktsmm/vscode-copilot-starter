---
applyTo: "**/*.bicep"
---

# Bicep ファイル用の指示

- モジュール分割を推奨（`modules/` ディレクトリ配下に配置）
- パラメータには `@description` デコレータで説明を付与
- シークレットは `@secure()` デコレータを使用
- `existing` キーワードで既存リソース参照
