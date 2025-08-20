---
allowed-tools: Bash(git:*, gh:*), Read, TodoWrite
description: gh-sub-issue拡張を使って正式なsub-issueを作成
---

# GitHubの正式なsub-issueを作成

## コンテキスト
- 親issue番号：$ARGUMENTS
- gh-sub-issue拡張を使用してGitHubが正式に認識するsub-issueを作成
- 現在のリポジトリでsub-issueを作成

## あなたのタスク

### 1. 前提条件の確認

- GitHub CLIが認証済みかを確認: `gh auth status`
- 現在のディレクトリがGitリポジトリかを確認: `git status`
- GitHubリポジトリと連携しているかを確認

### 2. gh-sub-issue拡張のインストール確認

- `gh extension list`で既にインストール済みかを確認
- インストールされていない場合: `gh extension install yahsan2/gh-sub-issue`
- インストール完了を確認

### 3. 親issueの情報取得

- `gh issue view $ARGUMENTS --json title,body,labels,assignees`で親issueの詳細を取得
- issueのタイトル、本文、ラベル、アサイニーを解析
- 親issueの内容から適切なsub-issueを設計

### 4. 正式なsub-issueの作成

- 親issueの内容を分析してsub-issueのタイトルと本文を生成
- `gh sub-issue create --parent $ARGUMENTS --title "具体的なタスクタイトル" --body "詳細な実装内容..."`でsub-issueを作成
- 必要に応じて複数のsub-issueを作成
- 各sub-issueに適切なラベルやアサイニーを設定

### 5. 作成結果の確認

- `gh issue list --search "parent:$ARGUMENTS"`で作成されたsub-issueを確認
- 各sub-issueがGitHub UIで親issueの子issueとして表示されることを確認
- sub-issueに「Parent issue」として親issueが表示されることを確認

### 6. 結果報告

- 作成されたsub-issueのURL一覧を表示
- 親issueとの正式な関連付け完了を報告
- TodoWriteで作業完了を記録

## 重要注意事項

- gh-sub-issue拡張が必要です（yahsan2/gh-sub-issue）
- GitHub CLIがインストールされ、認証済みである必要があります
- 現在のディレクトリがGitHubリポジトリと連携している必要があります
- $ARGUMENTSが有効なissue番号である必要があります
- この方式でのみGitHubが正式に認識するsub-issue構造が作成されます
- 通常の`gh issue create`では正式なsub-issueにはなりません
- 親issueが存在しない場合は適切なエラーメッセージを表示

## 使用例

```bash
# 基本的な使用方法
/create-sub-issue 2

# 結果として以下のようなコマンドが実行される：
# gh sub-issue create 2 --title "フロントエンド登録フォーム画面" --body "詳細な実装内容..."
# gh sub-issue create 2 --title "Supabase認証連携" --body "詳細な実装内容..."
# gh sub-issue create 2 --title "バリデーション機能" --body "詳細な実装内容..."
```

think hard