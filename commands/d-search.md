---
allowed-tools: Bash(gemini:*)
description: "Gemini CLIを使用して深度のあるウェブ検索を実行し、詳細なレポートを生成します"
---

## Gemini ディープサーチ

`gemini` は Google Gemini CLI です。ウェブ検索に使用できます。

### あなたのタスク（このワークフローに従う必要があります）:

1. 検索フェーズ: Gemini CLIを使用して複数の検索を並列実行

- Gemini CLIの`google_web_search`ツールを使用
- `>/search [引数]`のようなコマンドを受け取ります
  - 受け取った引数に対して、以下のようにGemini CLIのgoogle_web_searchツールを使用してください
  - !`gemini -m gemini-2.5-flash -p "google_web_search: [引数]'`
- 2-3個のキーワードを個別に並列検索（組み合わせではなく）
- 検索結果からURLのみを抽出（HTMLコンテンツは処理しない）
- 使用モデルは"gemini-2.5-flash"のみ

2. コンテンツ抽出フェーズ: readability MCPを使用してクリーンなコンテンツを抽出 `mcp__readability__read_url_content_as_markdown`

- ステップ1で得た最も関連性の高いURLに適用
- HTMLタグを除去し、メインコンテンツのみを抽出
- 重要: このツールを使用する前に検索結果を要約または処理しない（トークン消費を最小化するため）

3. レポート生成フェーズ: 抽出されたマークダウンコンテンツを包括的なレポートに統合

- 複数のソースからの情報を統合
- 構造化されたマークダウンレポートを作成

### 重要なルール

- Claudeのトークン消費を最小化するため、すべてのステップをTaskツール内で実行
- 組み込みの`Web Search`ツールは使用しない
- 生のHTML検索結果を直接処理または要約しない
- 処理前に常にreadabilityを使用してコンテンツを抽出
- 最終的な詳細レポートをマークダウンファイルに含める
