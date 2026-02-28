# ワークフローエディタ

ワークフローエディタでは、トリガーとアクションを組み合わせて自動化パイプラインをビジュアルに構築できます。

## 概要

コードを書かずにドラッグ＆ドロップでワークフローを構築：

```
[トリガー] → [アクション 1] → [アクション 2] → [通知]
```

## トリガー

ワークフローを開始する条件を設定します：

| トリガー | 説明 |
|---------|------|
| **Issue Created** | 新しい Issue が作成されたとき |
| **PR Merged** | PR がマージされたとき |
| **CI Failed** | CI/CD が失敗したとき |
| **Schedule** | 定期実行（cron 形式） |
| **Webhook** | 外部サービスからの Webhook |
| **Manual** | ダッシュボードから手動実行 |

## アクション

トリガー発火後に実行する操作を定義します：

| アクション | 説明 |
|-----------|------|
| **Run Tests** | テストを実行 |
| **Build** | ビルドを実行 |
| **Deploy** | デプロイを実行 |
| **Notify** | 通知を送信（Slack / Discord / Email） |
| **AI Analyze** | AI にコードを分析させる |
| **Create Issue** | 新しい Issue を自動作成 |
| **Create PR** | PR を自動作成 |

## ワークフロー例

### PR マージ後の自動テスト＆通知

```
[PR Merged]
  └─▶ [Run Tests]
        ├─ 成功 → [Notify: Slack] "テスト通過 ✅"
        └─ 失敗 → [Notify: Slack] "テスト失敗 ❌"
                   └─▶ [Create Issue] "テスト修正が必要"
```

### 定期セキュリティスキャン

```
[Schedule: 毎週月曜 9:00]
  └─▶ [AI Analyze: security]
        └─▶ [Create Issue] 検出された脆弱性ごとに Issue 作成
              └─▶ [Notify: Email] セキュリティレポート送信
```

### Issue 作成から完了までの完全自動化

```
[Issue Created: label="ados"]
  └─▶ [AI Implement]
        └─▶ [Run Tests]
              ├─ 成功 → [Create PR]
              │           └─▶ [Notify: Slack]
              └─ 失敗 → [AI Repair] → [Run Tests]（ループ）
```

## ダッシュボードでの使い方

1. **Workflows** タブに移動
2. **New Workflow** をクリック
3. トリガーを選択
4. アクションを追加・接続
5. 条件分岐を設定（任意）
6. **Save** をクリック

## ワークフローのステータス

| ステータス | 説明 |
|-----------|------|
| 🟢 Active | 有効化されて稼働中 |
| 🟡 Paused | 一時停止中 |
| 🔴 Error | エラーにより停止 |
| ⚪ Draft | 下書き（未有効化） |
