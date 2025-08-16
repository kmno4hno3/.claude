---
allowed-tools: Bash(git:*), Bash(gh:*), TodoWrite, TodoRead
description: Intelligently commit and push changes using GitHub CLI, automatically grouping related modifications into separate commits
---

## Smart Commit and Push with GitHub CLI

GitHub CLI (`gh`) を使用して変更内容を分析し、自動でコミットメッセージを生成してインテリジェントにグループ分けし、複数のコミットを作成してプッシュします。

Usage: 
- `/commit-push` (自動でコミットメッセージを生成)
- `/commit-push "<commit-message>"` (カスタムメッセージを指定)

Examples: 
- `/commit-push`
- `/commit-push "機能追加: ユーザー管理機能を実装"`

引数のコミットメッセージは `$ARGUMENTS` として利用可能です。引数が空の場合は変更内容から自動生成します。

## 処理フロー

### 1. 現在の状態を分析
- `gh repo view` でリポジトリ情報を確認
- `git status` で未追跡ファイルと変更ファイルを確認
- `git diff` で変更内容を確認
- `git log --oneline -5` で最近のコミット履歴を確認
- `gh auth status` でGitHub認証状態を確認
- 引数が空の場合、変更内容から適切なコミットメッセージを自動生成

### 2. 変更をグループ分け
変更内容を以下の基準で分類し、複数のコミットに分割するかを判断:

**分割基準:**
- **機能別**: 異なる機能や画面の変更は別コミット
- **ファイルタイプ別**: 設定ファイル、ソースコード、テスト、ドキュメントなど
- **変更性質別**: 新機能追加、バグ修正、リファクタリング、スタイル修正など
- **コンポーネント別**: 独立したコンポーネントやモジュールの変更

**単一コミットにする場合:**
- 単一機能に関連する変更のみ
- 変更ファイルが3個以下かつ関連性が高い
- すべてが同一のバグ修正や機能追加

### 3. コミット実行
各グループに対して：
- 関連ファイルをステージング: `git add <files>`
- 適切なコミットメッセージでコミット作成: `git commit -m "message"`
- コミットメッセージの末尾に標準フッターを追加

### 4. GitHub CLIを使用したプッシュとレポート
すべてのコミット完了後：
- `git push` でリモートリポジトリにプッシュ（GitHub CLIが認証を管理）
- `gh api repos/:owner/:repo/commits` で最新コミット情報を確認
- `gh repo view` で最新のリポジトリ状態を表示
- `gh pr list --state open` で関連PRの状況を確認
- `gh issue list --state open` で関連Issueも表示（該当する場合）
- プッシュ成功とGitHubでの反映状況を報告

## コミットメッセージフォーマット

基本形式：
```
<グループの説明>: <変更内容の要約>

🤖 Generated with [Claude Code](https://claude.ai/code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

**グループ別メッセージ例:**
- `機能追加: ユーザー認証機能を実装`
- `バグ修正: ログイン時のバリデーションエラーを修正`  
- `設定更新: ESLintとPrettierの設定を追加`
- `テスト追加: ユーザー管理機能のユニットテストを実装`
- `ドキュメント更新: README.mdにAPI仕様を追加`
- `リファクタリング: 共通コンポーネントを抽出`

## 自動コミットメッセージ生成

引数が提供されない場合、以下の情報を分析してコミットメッセージを自動生成します：

**分析対象:**
- 変更されたファイルパスとファイル名
- 追加・削除・変更された行の内容
- インポート文の変更
- 関数やクラスの追加・削除・変更
- 設定ファイルの変更内容

**生成パターン例:**
- 新規ファイル作成: `機能追加: UserService.tsを新規作成`
- 既存ファイル修正: `バグ修正: ログイン処理のバリデーションを修正`
- 複数ファイル変更: `機能追加: ユーザー認証機能の実装`
- 設定変更: `設定更新: TypeScript設定を更新`
- テスト追加: `テスト追加: UserServiceのユニットテストを実装`

## GitHub CLI活用

**認証確認:**
- `gh auth status` でGitHub認証を確認
- 未認証の場合は `gh auth login` の実行を提案

**リポジトリ情報:**
- `gh repo view` でリポジトリの詳細情報を表示
- `gh pr list` で現在のプルリクエスト状況を確認

**連携機能:**
- コミット後に関連するIssueやPRがあれば情報を表示
- ブランチ保護やレビュー要件も考慮した提案

## 重要な注意事項

- 変更内容をよく分析してから実行
- 敏感な情報（API キー、パスワードなど）が含まれていないか確認
- pre-commit フックでの変更があった場合は再コミット
- GitHub CLI認証が必要（`gh auth status`で確認）
- エラーが発生した場合は詳細を報告し、手動での対処方法を提案

think hard and work systematically