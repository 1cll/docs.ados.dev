# AI バックログ生成

AI バックログ生成は、リポジトリのコードを AI が分析し、改善 Issue を自動作成する機能です。

## 概要

AI がリポジトリ全体をスキャンし、以下の観点から改善提案を Issue として作成します：

- **セキュリティ** — 脆弱性やリスクの高いパターン
- **パフォーマンス** — ボトルネックや非効率なコード
- **コード品質** — 重複コード、複雑度の高い関数
- **テスト** — テストカバレッジの低い領域
- **ドキュメント** — ドキュメントの欠落

## 使い方

### ダッシュボードから

1. ダッシュボードで対象リポジトリを選択
2. **Backlog** タブに移動
3. **Generate Backlog** をクリック
4. AI がリポジトリを分析（数分かかる場合があります）
5. 生成された提案をレビュー
6. 必要な項目を選択し **Create Issues** をクリック

### API 経由

```bash
# バックログ生成を開始
POST /api/github/{owner}/{repo}/backlog/generate

# 生成結果を取得
GET /api/github/{owner}/{repo}/backlog
```

## 2 パス分析

バックログ生成は 2 段階の分析プロセスを使用します：

### パス 1：広域スキャン
- リポジトリのファイル構成をマッピング
- 主要ファイルを上位レベルで分析
- 技術スタックとアーキテクチャを把握

### パス 2：深層分析
- パス 1 の結果を基に重点領域を深堀り
- コード行レベルの改善提案を生成
- 優先度の付与（High / Medium / Low）

## フォーカス領域

分析のフォーカス領域をカスタマイズできます：

```yaml
repos:
  - name: my-repo
    workers:
      autopilot:
        focus:
          - security
          - performance
          - test-coverage
```

| フォーカス | 説明 |
|-----------|------|
| `security` | セキュリティ脆弱性とリスク |
| `performance` | パフォーマンス改善機会 |
| `code-quality` | コード品質と可読性 |
| `test-coverage` | テストカバレッジの改善 |
| `documentation` | ドキュメントの欠落 |
| `accessibility` | アクセシビリティの改善 |
| `refactoring` | リファクタリング提案 |

## カスタムプロンプト

独自の分析基準を追加できます：

```yaml
repos:
  - name: my-repo
    workers:
      autopilot:
        focus:
          - "API レスポンスフォーマットの一貫性をチェック"
          - "エラーハンドリングがベストプラクティスに従っているか確認"
```

## 生成される Issue のフォーマット

AI が生成する Issue には以下の情報が含まれます：

```markdown
## 問題
`src/services/auth.ts` の認証ロジックで
JWT トークンの有効期限チェックが実装されていません。

## 影響
期限切れのトークンでもアクセスが許可されるため、セキュリティリスクがあります。

## 修正案
- `validateToken()` 関数に有効期限チェックを追加
- 期限切れの場合は 401 Unauthorized を返す

## 対象ファイル
- `src/services/auth.ts` (L45-L60)
- `src/middleware/auth.middleware.ts` (L12)

## 優先度
🔴 High
```

> [!TIP]
> 生成された Issue には自動で `ados` ラベルが付与されるため、AutoPilot モードと組み合わせれば提案から実装まですべてを自動化できます。
