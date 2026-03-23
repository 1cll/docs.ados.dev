# スケジュール実行

ADOS は cron 式を使用して、定期的なメンテナンスタスクを自動実行できます。依存関係の更新、lint 修正、セキュリティスキャンなどの定期ジョブを定義すると、ADOS がスケジュールに従って GitHub Issue を作成し、AI エージェントが変更を実装して Pull Request を作成します。

## 概要

スケジュール実行は「設定したら放置」のワークフローを実現します：

1. **`ScheduledWatcher`** が毎分 cron 式をチェック
2. タスクの実行時刻になると、**GitHub Issue** が自動作成（`scheduled` ラベル付き）
3. **`IssueWatcher`** が Issue を検出し、AI エージェントを実行
4. AI が変更を実装し、**Pull Request** を作成

```
┌──────────────────┐     ┌──────────────┐     ┌──────────────┐     ┌─────────┐
│ ScheduledWatcher │ ──▶ │  GitHub Issue │ ──▶ │  IssueWatcher│ ──▶ │  PR     │
│ (cron トリガー)   │     │  (scheduled)  │     │  (AI エージェント) │     │         │
└──────────────────┘     └──────────────┘     └──────────────┘     └─────────┘
```

## 対応タスクタイプ

| タイプ | 説明 | デフォルトの Issue タイトル |
|--------|------|---------------------------|
| `dependency-update` | 依存関係の更新 | `[Scheduled] Update dependencies` |
| `lint-fix` | lint 問題の自動修正 | `[Scheduled] Fix lint issues` |
| `security-scan` | セキュリティ監査 | `[Scheduled] Security scan and fixes` |
| `custom` | カスタムタスク | ユーザー定義 |

## Cron 式

標準的な 5 フィールドの cron 構文をサポートしています：

```
 ┌───────────── 分 (0-59)
 │ ┌───────────── 時 (0-23)
 │ │ ┌───────────── 日 (1-31)
 │ │ │ ┌───────────── 月 (1-12)
 │ │ │ │ ┌───────────── 曜日 (0-6, 日=0)
 │ │ │ │ │
 * * * * *
```

### よく使うスケジュール

| 式 | スケジュール |
|----|-------------|
| `0 0 * * 0` | 毎週日曜 00:00 |
| `0 9 * * 1` | 毎週月曜 09:00 |
| `0 0 1 * *` | 毎月 1 日 00:00 |
| `0 0 * * *` | 毎日 00:00 |

## API エンドポイント

### スケジュールタスク一覧

```bash
GET /api/v1/settings/repos/{id}/scheduled-tasks
```

### タスク作成

```bash
POST /api/v1/settings/repos/{id}/scheduled-tasks
```

```json
{
  "enabled": true,
  "task_type": "dependency-update",
  "cron_expression": "0 0 * * 0",
  "timezone": "Asia/Tokyo"
}
```

### タスク更新

```bash
PATCH /api/v1/settings/repos/{id}/scheduled-tasks/{taskId}
```

### タスク削除

```bash
DELETE /api/v1/settings/repos/{id}/scheduled-tasks/{taskId}
```

### 手動トリガー

```bash
POST /api/v1/settings/repos/{id}/scheduled-tasks/{taskId}/trigger
```

## ダッシュボードでの設定

タスクはダッシュボードの GUI からも設定できます：

**Settings > Scheduled Tasks**

1. リポジトリ設定ページに移動
2. **Scheduled Tasks** タブを開く
3. **Add Task** をクリックしてタスクタイプを選択
4. cron 式とタイムゾーンを設定
5. 保存

## ユースケース

### 毎週の依存関係更新
毎週日曜の夜に `dependency-update` をスケジュール。月曜の朝にはパッケージ更新済みの PR がレビュー待ちになっています。

### 日次 lint メンテナンス
毎日 `lint-fix` を実行して、手作業なしでコードベースをクリーンに保ちます。

### 月次セキュリティ監査
毎月 1 日に `security-scan` を実行して、脆弱性を早期に発見します。

### カスタムメンテナンス
`custom` タイプを使用して、プロジェクト固有の定期タスクをユーザー定義のタイトルと手順で実行できます。

> [!TIP]
> スケジュール実行と [AutoPilot モード](ja/guides/autopilot.md) を組み合わせると、リポジトリの完全自律メンテナンスが実現できます。
