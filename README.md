# Claude Code 設定ベストプラクティス

Claude Code の設定とカスタマイズのベストプラクティスを収集したリポジトリです。このリポジトリは継続的に更新・改善し、より良いものにしていきます。

**注意:** このリポジトリの一部の設定は日本語ユーザー向けに特別に設定されています。お使いの環境に合わせて適切に翻訳・調整してください。

このリポジトリの設定ファイルは `~/.claude/` ディレクトリ以下に配置することを想定して設計されています。これらの設定ファイルを適切な場所に配置することで、Claude Code の動作をカスタマイズし、効率的な開発環境を構築できます。

## プロジェクト構造

```
claude-code-settings/
├── CLAUDE.md          # ~/.claude/ 配置用のグローバルユーザーガイドライン
├── settings.json      # Claude Code 設定ファイル
├── commands/          # カスタムコマンド定義
│   ├── code-review.md    # 詳細分析を含むコードレビューの実行
│   ├── d-search.md       # gemini-cli を使った深度のあるコードベース分析
│   ├── design.md         # 技術設計フェーズの実行
│   ├── marp.md          # Marp プレゼンテーション作成コマンド
│   ├── requirements.md   # 要件定義フェーズの実行
│   ├── search.md        # gemini-cli を使った Google ウェブ検索
│   ├── spec.md          # 完全な仕様駆動開発ワークフロー
│   ├── tasks.md         # タスク分割フェーズの実行
│   └── textlint.md      # textlint によるファイル校正・修正
└── symlinks/         # 外部ツール設定ファイルのシンボリックリンク
    ├── settings.json      # MCP 設定を含む Claude Code 設定
    └── config/
        └── ccmanager/
            └── config.json    # ccmanager: Claude Code プロジェクト & git worktree マネージャー
```

## symlinks フォルダについて

`symlinks/` フォルダには、Claude Code に関連する様々な外部ツールの設定ファイルが含まれています。Claude Code は頻繁に更新され、設定変更が多いため、すべての設定ファイルを一つのフォルダに集約することで編集がとても楽になります。通常は `~/.claude/` ディレクトリ外に置かれる関連ファイルでも、シンボリックリンクとしてここに配置することで統一的に管理できて便利です。

実際の環境では、これらのファイルは指定の場所にシンボリックリンクとして配置されます。

```bash
# Claude Code 設定のリンク
ln -s /path/to/settings.json ~/.claude/settings.json

# ccmanager 設定のリンク
ln -s /path/to/.config/ccmanager/config.json ~/.claude/symlinks/ccmanager/config.json
```

これにより、設定変更をリポジトリで管理し、複数の環境で共有することができます。

## 主要機能

### 1. 仕様駆動開発ワークフロー

このプロジェクトの最大の特徴は、4段階の仕様駆動開発ワークフローです：

1. **要件定義** (`/requirements`) - ユーザーのリクエストを明確な機能要件に変換
2. **設計** (`/design`) - 技術設計とアーキテクチャの策定
3. **タスク分割** (`/tasks`) - 実装可能な単位にタスクを分割
4. **実装** - タスクリストに基づく体系的な実装

**注意:** これらのスラッシュコマンドで生成される設計書は、各コマンドに設定されているプロンプトにより日本語で出力されます。

### 2. 効率的な開発ルール

- **並列処理の活用**: 複数の独立プロセスを同時実行
- **英語思考・日本語回答**: 内部処理は英語、ユーザー回答は日本語
- **Context7 MCP の活用**: 常に最新のライブラリ情報を参照
- **徹底的な検証**: Write/Edit 後は必ず Read で検証

## ファイル詳細

### CLAUDE.md

プロジェクト固有のガイドラインを定義します。以下の内容を含みます：

- **トップレベルルール**: 基本的な動作ルール
- **プログラミングルール**: コーディング規約（TypeScript 使用時など）
- **開発スタイル**: 詳細な仕様駆動開発ワークフロー

### settings.json

Claude Code の動作を制御する設定ファイル：

#### 環境変数設定 (`env`)
```json
{
  "DISABLE_TELEMETRY": "1",        // テレメトリー無効化
  "DISABLE_ERROR_REPORTING": "1",   // エラーレポート無効化
  "API_TIMEOUT_MS": "600000"        // API タイムアウト（10分）
}
```

#### 権限設定 (`permissions`)

**allow（許可リスト）**:
- ファイル読み取り: `Read(**)`
- 特定ディレクトリへの書き込み: `Write(src/**)`, `Write(docs/**)`, `Write(.tmp/**)`
- Git 操作: `git init`, `git add`, `git commit`, `git push origin*`
- パッケージ管理: `npm install`, `pnpm install`
- MCP 関連: Context7、Playwright などのツール使用

**deny（禁止リスト）**:
- 危険なコマンド: `sudo`, `rm -rf`
- セキュリティ関連: `.env.*` ファイルの読み取り、`id_rsa` など
- データベース直接操作: `psql`, `mysql` など

#### フック設定 (`hooks`)

**PostToolUse**（ツール使用後の自動処理）
- コマンド履歴の記録（Bash、Read、Write など）
- Markdown ファイル編集時の textlint 自動実行

**Notification**（通知設定 - macOS）
- 作業進捗の通知表示

**Stop**（作業完了時の処理）
- 完了通知の表示

#### MCP サーバー設定 (`enabledMcpjsonServers`)
- GitHub 連携（複数アカウント対応）
- Context7（ドキュメント取得）
- Playwright（ブラウザ自動化）
- Readability（ウェブ記事読み取り）
- textlint（日本語校正）

### カスタムコマンド (commands/)

| コマンド        | 説明                                               |
| --------------- | -------------------------------------------------- |
| `/spec`         | 完全な仕様駆動開発ワークフロー                      |
| `/requirements` | 要件定義フェーズの実行                              |
| `/design`       | 技術設計フェーズの実行                              |
| `/tasks`        | タスク分割フェーズの実行                            |
| `/code-review`  | 詳細分析を含むコードレビューの実行                   |
| `/search`       | gemini-cli を使った Google ウェブ検索               |
| `/d-search`     | gemini-cli を使った深度のあるコードベース分析        |
| `/marp`         | Marp プレゼンテーション作成コマンド                  |
| `/textlint`     | textlint によるファイル校正・修正                   |

## セットアップ

### 1. リポジトリのクローン

```bash
git clone https://github.com/nokonoko1203/claude-code-settings.git
cd claude-code-settings
```

### 2. Claude Code への設定適用

リポジトリの内容を `~/.claude/` にコピーするか、リポジトリと同期を保つためにシンボリックリンクを作成することができます。

#### オプション A: ~/.claude/ にコンテンツをコピー
```bash
# 設定ファイルを ~/.claude/ ディレクトリにコピー
cp CLAUDE.md ~/.claude/
cp settings.json ~/.claude/
cp .textlintrc.json ~/.claude/
cp -r commands ~/.claude/
cp -r symlinks ~/.claude/
```

#### オプション B: リポジトリを ~/.claude/ にリンク（推奨）
```bash
# リポジトリとの同期を保つためのシンボリックリンクを作成
ln -s /path/to/claude-code-settings ~/.claude/claude-code-settings
# 各ファイルを個別にリンク
ln -s ~/.claude/claude-code-settings/CLAUDE.md ~/.claude/
ln -s ~/.claude/claude-code-settings/settings.json ~/.claude/
ln -s ~/.claude/claude-code-settings/commands ~/.claude/
```

### 3. シンボリックリンクを使用した外部ツールの設定

一元管理のため、外部ツールの場所から `~/.claude/symlinks/` へのシンボリックリンクを作成：

```bash
# symlinks ディレクトリ構造を作成
mkdir -p ~/.claude/symlinks/config/ccmanager/

# Claude Code グローバル設定を symlinks フォルダにリンク
ln -s ~/claude.json ~/.claude/symlinks/claude.json

# ccmanager 設定を symlinks フォルダにリンク
ln -s ~/.config/ccmanager/config.json ~/.claude/symlinks/config/ccmanager/config.json
```

このアプローチにより、すべての Claude Code 関連設定ファイルを `~/.claude/` ディレクトリに集約し、管理を容易にします。

## 参考資料

- [Claude Code overview](https://docs.anthropic.com/en/docs/claude-code)
- [Model Context Protocol (MCP)](https://docs.anthropic.com/en/docs/mcp)
- [textlint](https://textlint.github.io/)
- [CCManager](https://github.com/kbwo/ccmanager)
- [Context7](https://context7.com/)

## ライセンス

このプロジェクトは MIT ライセンスの下でリリースされています。
