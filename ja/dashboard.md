# ダッシュボード概要

ADOS ダッシュボードは、すべての機能にアクセスできる Web ベースの管理コンソールです。

## ページ一覧

| ページ | 説明 |
|--------|------|
| **Dashboard** | ジョブ統計、成功率、トレンドチャート、リポジトリカード |
| **Pull Requests** | AI が生成した PR 一覧、ステータス表示、マージ操作 |
| **Monitor** | GitHub Actions のステータス、セキュリティアラート |
| **Runners** | セルフホスト Runner の管理（Issue / Chat / Pipeline） |
| **Usage** | AI トークン使用量、コスト見積もり |
| **Feedback** | AI エージェントの品質フィードバック（PR マージ/リジェクト分析） |
| **Logs** | サービスログの閲覧、検索、フィルタリング |
| **Settings** | リポジトリ設定、GitHub 連携、通知、請求管理 |

## Dashboard ページ

メインダッシュボードでは以下の情報を表示します：

- **リポジトリカード** — リポジトリごとの Issue ステータス（Open / Queued / Needs Attention）
- **ジョブ統計** — 直近の処理件数と成功率
- **トレンドチャート** — 日別/週別の処理推移
- **エージェント性能** — Copilot / Claude / Codex の比較

## Pull Requests ページ

AI が作成した Pull Request の一覧を表示します。

- **ステータス**: Open / Merged / Closed
- **ワンクリックマージ**: ダッシュボードから直接マージまたはスカッシュ
- **Diff プレビュー**: 変更の概要

## Usage ページ

AI エージェントのトークン使用量とコストを可視化します。

- **日別使用量チャート**
- **エージェント別内訳**
- **予算設定** — 月次予算の上限を設定し、超過時にアラート

## Settings ページ

### リポジトリ設定

- リポジトリの追加・編集・削除
- ラベル、ターゲットブランチ、デフォルトエージェントの設定
- Issue ルーティングルール

### GitHub 連携

- GitHub App のインストールと管理
- Personal Access Token（PAT）の登録
- 接続ステータスの確認

### 通知設定

- Slack / Discord / Email の通知設定
- 通知ルール（重要度フィルタリング）
- テスト通知の送信

### 請求管理

- 現在のプランを表示
- プランのアップグレード/ダウングレード
- 請求履歴の確認

> [!TIP]
> ダッシュボードには [https://ados-platform-dashboard.web.app](https://ados-platform-dashboard.web.app) からアクセスできます。
