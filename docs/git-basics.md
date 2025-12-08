# Git & GitHub 基礎ガイド

Git と GitHub の基本概念とよく使うコマンドをまとめました。

---

## 📑 目次

- [Git と GitHub の違い](#git-と-github-の違い)
- [基本概念](#-基本概念)
- [基本コマンド](#-基本コマンド)
- [ブランチ操作](#-ブランチ操作)
- [リモートリポジトリ操作](#-リモートリポジトリ操作)
- [GitHub CLI（gh）](#-github-cligh)
- [よくあるワークフロー](#-よくあるワークフロー)
- [トラブルシューティング](#-トラブルシューティング)

---

## Git と GitHub の違い

| 項目           | Git                                    | GitHub                               |
| -------------- | -------------------------------------- | ------------------------------------ |
| **種類**       | バージョン管理システム（ソフトウェア） | Git リポジトリのホスティングサービス |
| **場所**       | ローカル PC                            | クラウド（Web）                      |
| **役割**       | コードの変更履歴を管理                 | コードの共有・コラボレーション       |
| **オフライン** | 使用可能                               | インターネット接続が必要             |

```
┌─────────────────┐     push      ┌─────────────────┐
│   ローカル PC    │  ─────────→  │     GitHub      │
│     (Git)       │  ←─────────  │  (リモート)      │
└─────────────────┘     pull      └─────────────────┘
```

---

## 📚 基本概念

### リポジトリ（Repository）

プロジェクトのファイルと変更履歴を保存する場所。

- **ローカルリポジトリ**: 自分の PC 上にあるリポジトリ
- **リモートリポジトリ**: GitHub 上にあるリポジトリ

### コミット（Commit）

ファイルの変更を記録するスナップショット。「セーブポイント」のようなもの。

```
コミット1 → コミット2 → コミット3 → ... （時系列）
```

### ステージング（Staging）

コミットする前に、変更をまとめる準備エリア。

```
作業ディレクトリ  →  ステージング  →  コミット
  (編集中)         (add で追加)     (commit で確定)
```

### ブランチ（Branch）

開発の流れを分岐させる機能。メインのコードに影響を与えずに作業できる。

```
main ─────●─────●─────●─────●───→
                \           /
feature/login    ●────●────●
                (機能開発)
```

### マージ（Merge）

ブランチの変更を別のブランチに統合すること。

### プルリクエスト（Pull Request / PR）

GitHub 上で「このブランチの変更を取り込んでほしい」とリクエストする機能。コードレビューに使用。

---

## 💻 基本コマンド

### 初期設定（最初に 1 回だけ）

```powershell
# ユーザー名とメールアドレスを設定（コミット履歴に記録される）
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 設定確認
git config --list
```

### リポジトリの作成・クローン

```powershell
# 新規リポジトリを作成（現在のフォルダを Git 管理下に）
git init

# 既存のリポジトリをコピー
git clone https://github.com/owner/repo.git
```

### 状態確認

```powershell
# 現在の状態を確認（変更ファイル、ブランチなど）
git status

# コミット履歴を確認
git log --oneline

# 変更内容を確認
git diff
```

### 変更をコミット

```powershell
# 1. 変更をステージングに追加
git add ファイル名        # 特定のファイル
git add .                # すべての変更

# 2. コミット（変更を記録）
git commit -m "コミットメッセージ"

# ステージング + コミットを一度に（追跡済みファイルのみ）
git commit -am "コミットメッセージ"
```

### コミットメッセージの書き方（Conventional Commits）

```
feat: 新機能を追加
fix: バグ修正
docs: ドキュメントのみの変更
style: コードの意味に影響しない変更（空白、フォーマットなど）
refactor: バグ修正や機能追加を伴わないコード変更
test: テストの追加・修正
chore: ビルドプロセスやツールの変更
```

例:

```powershell
git commit -m "feat: ユーザー認証機能を追加"
git commit -m "fix: ログイン時のエラーハンドリングを修正"
git commit -m "docs: README にインストール手順を追加"
```

---

## 🌿 ブランチ操作

### ブランチの基本

```powershell
# ブランチ一覧を表示
git branch

# リモートブランチも含めて表示
git branch -a

# 新しいブランチを作成
git branch feature/new-feature

# ブランチを切り替え
git checkout feature/new-feature

# ブランチ作成 + 切り替えを一度に
git checkout -b feature/new-feature

# ブランチを削除（マージ済み）
git branch -d feature/new-feature

# ブランチを強制削除（マージしていなくても）
git branch -D feature/new-feature
```

### ブランチ命名規則

| プレフィックス | 用途             | 例                      |
| -------------- | ---------------- | ----------------------- |
| `feature/`     | 新機能開発       | `feature/user-auth`     |
| `fix/`         | バグ修正         | `fix/login-error`       |
| `docs/`        | ドキュメント更新 | `docs/update-readme`    |
| `refactor/`    | リファクタリング | `refactor/cleanup-api`  |
| `hotfix/`      | 緊急修正         | `hotfix/security-patch` |

### マージ

```powershell
# main ブランチに切り替え
git checkout main

# feature ブランチを main にマージ
git merge feature/new-feature
```

---

## 🌐 リモートリポジトリ操作

### リモートの確認・設定

```powershell
# リモートリポジトリを確認
git remote -v

# リモートを追加
git remote add origin https://github.com/owner/repo.git

# リモート URL を変更
git remote set-url origin https://github.com/owner/new-repo.git
```

### プッシュ（ローカル → リモート）

```powershell
# 変更をリモートに送信
git push origin main

# 初回プッシュ（上流ブランチを設定）
git push -u origin main

# 以降は省略可能
git push
```

### プル（リモート → ローカル）

```powershell
# リモートの変更を取得してマージ
git pull origin main

# 上流ブランチが設定されていれば省略可能
git pull
```

### フェッチ（リモートの情報を取得、マージはしない）

```powershell
# リモートの最新情報を取得
git fetch origin

# リモートブランチを確認してからマージ
git fetch origin
git merge origin/main
```

---

## 🔧 GitHub CLI（gh）

GitHub CLI を使うと、ターミナルから GitHub の操作ができます。

### 認証

```powershell
# GitHub にログイン
gh auth login

# 認証状態を確認
gh auth status
```

### リポジトリ操作

```powershell
# リポジトリをクローン
gh repo clone owner/repo

# 新しいリポジトリを作成
gh repo create my-repo --public --source=. --push

# リポジトリを Web ブラウザで開く
gh repo view --web
```

### プルリクエスト操作

```powershell
# PR を作成
gh pr create --title "機能追加" --body "説明"

# PR 一覧を表示
gh pr list

# PR をマージ
gh pr merge 123

# PR を Web ブラウザで開く
gh pr view --web
```

### Issue 操作

```powershell
# Issue を作成
gh issue create --title "バグ報告" --body "説明"

# Issue 一覧を表示
gh issue list

# Issue をクローズ
gh issue close 123
```

---

## 🔄 よくあるワークフロー

### 新機能を開発する流れ

```powershell
# 1. main ブランチを最新に
git checkout main
git pull

# 2. 機能ブランチを作成
git checkout -b feature/new-feature

# 3. コードを編集...

# 4. 変更をコミット
git add .
git commit -m "feat: 新機能を追加"

# 5. リモートにプッシュ
git push -u origin feature/new-feature

# 6. GitHub で PR を作成
gh pr create --title "新機能を追加" --body "説明"

# 7. レビュー後、PR をマージ
gh pr merge --squash

# 8. ローカルのブランチを削除
git checkout main
git pull
git branch -d feature/new-feature
```

### 他の人の変更を取り込む

```powershell
# main ブランチの最新を取得
git checkout main
git pull

# 作業ブランチに main の変更を取り込む
git checkout feature/my-feature
git merge main
# または
git rebase main
```

---

## 🚨 トラブルシューティング

### 直前のコミットを修正したい

```powershell
# コミットメッセージを修正
git commit --amend -m "新しいメッセージ"

# ファイルを追加してコミットし直し
git add forgotten-file.txt
git commit --amend --no-edit
```

### 変更を一時退避したい（stash）

```powershell
# 変更を退避
git stash

# 退避した変更を戻す
git stash pop

# 退避一覧を確認
git stash list
```

### コミットを取り消したい

```powershell
# 直前のコミットを取り消し（変更は残る）
git reset --soft HEAD~1

# 直前のコミットを取り消し（変更も消える）
git reset --hard HEAD~1
```

### マージコンフリクトが発生したら

```powershell
# 1. コンフリクトしたファイルを確認
git status

# 2. ファイルを開いて手動で修正
#    <<<<<<< HEAD
#    自分の変更
#    =======
#    相手の変更
#    >>>>>>> branch-name

# 3. 修正後、ステージングに追加
git add conflicted-file.txt

# 4. マージを完了
git commit -m "Merge branch 'branch-name'"
```

### リモートの変更で上書きしたい

```powershell
# ローカルの変更を破棄してリモートに合わせる
git fetch origin
git reset --hard origin/main
```

---

## 📚 参考リンク

- [Git 公式ドキュメント](https://git-scm.com/doc)
- [GitHub Docs](https://docs.github.com/ja)
- [GitHub CLI マニュアル](https://cli.github.com/manual/)
- [Conventional Commits](https://www.conventionalcommits.org/ja/)

---

[← README に戻る](../README.MD)
