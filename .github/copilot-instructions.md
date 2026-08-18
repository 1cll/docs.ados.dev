# docs.ados.dev — Copilot 向け指示書

## プロジェクト概要

- **用途**: ADOS プラットフォームのユーザー向けドキュメント
- **技術スタック**: Docsify 静的サイト
- **言語**: 3 言語対応（英語: ルート, 日本語: `ja/`, ロシア語: `ru/`）
- **ホスティング**: GitHub Pages
- **URL**: `https://docs.ados.dev`

## ドキュメント構成

```
/ (英語)
├── reference/
│   ├── api.md        — API リファレンス
│   ├── config.md     — YAML 設定リファレンス
│   ├── pricing.md    — 料金プラン
│   └── env-vars.md   — 環境変数
├── guides/
│   ├── issue-automation.md  — Issue 自動処理
│   ├── workflows.md         — マルチステージワークフロー
│   ├── pipeline-watcher.md  — CI/CD 監視
│   ├── agentic-chat.md      — Agentic Chat
│   └── autopilot.md         — AutoPilot モード
├── self-host/
│   ├── docker.md     — Docker セットアップ
│   └── runners.md    — セルフホステッドランナー
├── dashboard.md      — ダッシュボード
└── getting-started.md — スタートガイド
ja/ (日本語 — 同一構成)
ru/ (ロシア語 — 同一構成)
```

## ADOS 本体との整合性メモ

ドキュメントの記述は以下の正しい値と必ず一致させること:

| 項目 | 正しい値 |
|---|---|
| API ベース URL | `https://api.ados.dev/api/v1` |
| 認証方式 | Firebase ID Token（`Authorization: Bearer <token>`） |
| エラー形式 | `{"error": "<message>"}` （文字列、ネストしたオブジェクトではない） |
| プラン数 | 4（free / pro / team / enterprise） |
| Enterprise 月額 | $499 |
| Pro 最大エージェント | 2 |
| ジョブロック TTL | 45 分 |
| ジョブステータス | `running` / `completed` / `failed` / `needs-human` / `reset`（`queued`/`in_progress` は存在しない） |
| AutoPilot max_per_cycle | 3 |
| AutoPilot max_per_day | 10 |
| AutoPilot cooldown | 1h |
| execution_preference デフォルト | `"auto"` |
| Workflow ステージ | Plan → Approval → Implement → Review（ドラッグ＆ドロップではない） |
| `gcloud_projects` 型 | `map[string]string` |
| Docker イメージ (Work Runner) | `asia-northeast1-docker.pkg.dev/ados-platform/ados/ados-work-runner:latest` |
| CI/CD 対応 | GitHub Actions のみ（CircleCI/Jenkins は未対応） |
| Webhook dead letter コレクション名 | `webhook_deadletters`（アンダースコアなし） |
| cost_summary コレクション | Deprecated（`cost_summaries` を使用） |

## 関連リポジトリ

| リポジトリ | 場所 | 役割 |
|---|---|---|
| ADOS 本体 | `~/src/ADOS` | メインプロダクト（API + Worker + Runner） |
| ados-admin | `~/src/ados-admin` | 管理ポータル（同一 Firestore を共有） |
| docs.ados.dev | `~/src/docs.ados.dev` | ユーザー向けドキュメント（本リポジトリ） |

## 編集ルール

- **3 言語同時更新**: 英語を変更した場合、`ja/` と `ru/` の対応ファイルも必ず更新すること
- **値の変更は ADOS 本体のコードを確認**: API エンドポイント、プラン定義、設定デフォルト値などを記述する際は ADOS 本体の実装コードで正しい値を確認すること
- **CLI コマンドは実在するものだけ記載**: `ados start`、`ados config validate` など実装されていないコマンドを記載しない
