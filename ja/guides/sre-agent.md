# SRE エージェント

SRE エージェントは本番環境のメトリクスを監視し、異常検知時に自動で調査・修復を行います。

## 概要

```
┌──────────────┐     ┌───────────────┐     ┌──────────────┐     ┌──────────────┐
│  メトリクス    │ ──▶ │  異常検知      │ ──▶ │  原因分析     │ ──▶ │  修正 PR     │
│ （Cloud Logs） │     │  （閾値）      │     │  （AI）       │     │  （自動）     │
└──────────────┘     └───────────────┘     └──────────────┘     └──────────────┘
```

## 有効化

```yaml
repos:
  - name: my-repo
    workers:
      sre_agent:
        enabled: true
        gcloud_projects:
          - my-gcp-project-id
        error_threshold: 10        # エラーレート閾値（/分）
        latency_threshold_ms: 5000 # レイテンシ閾値（ms）
        check_interval: 5m         # チェック間隔
        cooldown_duration: 1h      # 重複アラート防止のクールダウン
```

## 監視メトリクス

### エラーレート
- アプリケーションのエラーログ頻度を監視
- `error_threshold` を超えるとアラート発報

### レイテンシ
- API のレスポンスタイムを監視
- `latency_threshold_ms` を超えるとアラート発報

### リソース
- メモリ使用量（OOM Kill 検知）
- CPU 使用率
- ディスク使用量

### アプリケーションログ
- GCP Cloud Logging からログを取得
- スタックトレースの自動解析
- パターンベースの異常検知

## GCP 連携

SRE エージェントは現在、以下の **Google Cloud Platform** サービスと連携します：

| サービス | 用途 |
|---------|------|
| **Cloud Logging** | アプリケーションログの取得 |
| **Cloud Monitoring** | メトリクスの取得 |
| **Cloud Run** | コンテナ環境の監視 |

### 認証設定

```bash
# 環境変数で GCP プロジェクトを設定
export GCP_PROJECT=my-project-id
export GOOGLE_CLOUD_PROJECT=my-project-id
```

> [!NOTE]
> AWS および Azure のサポートは今後のリリースで予定しています。

## 自動修復の例

### 1. エラーレートの急増

```
検知: /api/users でエラーレートが 50/分を超過
分析: db.query() で NullPointerException
原因: 新規デプロイ後に null チェックが不足
修正: null チェックを追加する PR を自動作成
```

### 2. メモリリーク（OOM）

```
検知: OOM Kill によりコンテナが再起動
分析: ログから過剰なオブジェクト生成パターンを検出
原因: キャッシュにサイズ制限がなかった
修正: LRU キャッシュ制限を追加する PR を自動作成
```

### 3. レイテンシ悪化

```
検知: /api/search のレイテンシが 5000ms を超過
分析: 新しいクエリにインデックスがない
原因: 新しいカラムのクエリ追加時にインデックス未作成
修正: インデックスマイグレーションの PR を自動作成
```

## アラート通知

SRE エージェントのアラートは通知システムと連携できます：

- **Slack** — チャンネルに即時通知
- **Discord** — Webhook 通知
- **Email** — アラートメール

詳しくは [通知設定](ja/guides/notifications.md) を参照してください。

## プラン別利用可否

SRE エージェントは **Pro プラン以上** で利用可能です。

| プラン | SRE エージェント |
|--------|----------------|
| Free | ❌ |
| Pro | ✅ |
| Team | ✅ |
