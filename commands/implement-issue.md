---
allowed-tools: Bash(git:*, gh:*), Read, Write, Edit, MultiEdit, Glob, Grep, TodoWrite, TodoRead
description: 指定されたissue番号の内容を分析して実装・実行する
---

# GitHubのissueを分析して実装・実行

## コンテキスト
- 対象issue番号：$ARGUMENTS
- issueの内容を分析して適切な実装計画を立案
- 実装から検証まで一貫して実行

## あなたのタスク

### 1. 前提条件の確認

- GitHub CLIが認証済みかを確認: `gh auth status`
- 現在のディレクトリがGitリポジトリかを確認: `git status`
- GitHubリポジトリと連携しているかを確認

### 2. issue情報の取得と分析

- `gh issue view $ARGUMENTS --json title,body,labels,assignees,state`でissueの詳細を取得
- issueのタイトル、本文、ラベル、状態を分析
- 実装に必要な要件を抽出

### 3. 実装計画の立案

- TodoWriteツールを使用して実装タスクを計画
- issue内容から以下を特定：
  - 実装すべき機能
  - 修正すべきバグ
  - 必要なファイル変更
  - テスト要件
- 優先度順にタスクを整理

### 4. 技術調査

- 関連するファイルやコードベースを調査
- GlobやGrepツールを使用して関連コードを特定
- 既存の実装パターンや規約を確認

### 5. 実装の実行

- 計画したタスクを順次実行
- 各タスクをin_progressに更新してから実装開始
- 実装完了後にcompletedに更新
- コード品質を保つために：
  - 既存のコード規約に従う
  - 適切なエラーハンドリングを実装
  - 必要に応じてコメントを追加

### 6. テストと検証

- 実装した機能のテスト実行
- lintやtypecheckの実行（該当する場合）
- 動作確認の実施

### 7. 結果報告とissue更新

- 実装内容の概要を報告
- 変更されたファイルの一覧を表示
- `gh issue comment $ARGUMENTS --body "実装完了: [実装内容の概要]"`でissueにコメント追加
- 必要に応じてissueをcloseまたは適切な状態に更新

## 重要注意事項

- GitHub CLIがインストールされ、認証済みである必要があります
- 現在のディレクトリがGitHubリポジトリと連携している必要があります
- $ARGUMENTSが有効なissue番号である必要があります
- issue内容が不明確な場合は、明確化のためのコメントを追加
- 大規模な変更の場合は、段階的に実装を進める
- セキュリティに関わる変更は特に慎重に実装
- 破壊的変更の可能性がある場合は事前に確認

## 実装スタイル

- Specification-Driven Developmentに従って進める
- 複雑な実装の場合は要件→設計→テスト設計→実装の順で進める
- 簡単な修正の場合は直接実装を開始
- 必ずTodoWriteで進捗を管理

## 使用例

```bash
# 基本的な使用方法
/implement-issue 15

# 結果として以下のような流れで実行される：
# 1. issue #15の内容を取得・分析
# 2. 実装計画をTodoWriteで管理
# 3. 必要なファイルを特定・実装
# 4. テスト実行
# 5. issue #15にコメントして完了報告
```

think hard and implement step by step