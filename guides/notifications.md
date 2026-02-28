# 通知設定

ADOS は処理の進捗や結果を Slack / Discord / メール で通知できます。

## 対応チャンネル

| チャンネル | 設定方法 |
|-----------|---------|
| **Slack** | Webhook URL |
| **Discord** | Webhook URL |
| **Email** | メールアドレス |

## Slack 連携

### 1. Incoming Webhook の作成

1. [Slack API](https://api.slack.com/apps) で新しいアプリを作成
2. **Incoming Webhooks** を有効化
3. **Add New Webhook to Workspace** でチャンネルを選択
4. Webhook URL をコピー

### 2. ADOS に登録

1. ダッシュボードの **Settings** → **Notifications** に移動
2. **Slack** タブを選択
3. Webhook URL を貼り付け
4. **テスト通知を送信** で確認
5. **保存**

### 通知の例

```
🚀 ADOS | Issue #42 を処理中
リポジトリ: my-org/my-repo
タイトル: ログイン API にレートリミットを追加
エージェント: Claude (claude-sonnet-4)
ステータス: 実装中...
```

```
✅ ADOS | PR を作成しました
リポジトリ: my-org/my-repo
PR #123: ログイン API にレートリミットを追加
変更ファイル: 3 files (+45 -12)
レビューする → https://github.com/my-org/my-repo/pull/123
```

## Discord 連携

### 1. Webhook の作成

1. Discord サーバー設定 → **連携サービス** → **ウェブフック**
2. **新しいウェブフック** をクリック
3. チャンネルを選択
4. **ウェブフック URL をコピー**

### 2. ADOS に登録

1. ダッシュボードの **Settings** → **Notifications** に移動
2. **Discord** タブを選択
3. Webhook URL を貼り付け
4. **保存**

## Email 通知

1. ダッシュボードの **Settings** → **Notifications** に移動
2. **Email** タブを選択
3. 通知先メールアドレスを追加
4. **保存**

## 通知イベント

通知するイベントをカスタマイズできます：

| イベント | デフォルト | 説明 |
|---------|-----------|------|
| Issue 処理開始 | ✅ | AI が Issue の処理を開始 |
| PR 作成 | ✅ | PR が作成された |
| CI 成功 | ❌ | CI/CD が成功した |
| CI 失敗 | ✅ | CI/CD が失敗した |
| 自動修復 | ✅ | Pipeline Watcher が修復を実行 |
| SRE アラート | ✅ | SRE エージェントがアラートを発火 |
| バックログ生成完了 | ❌ | AI バックログの生成が完了 |
| エラー | ✅ | 処理中にエラーが発生 |

## 通知の頻度制限

大量の通知でチャンネルが埋もれないよう、頻度制限を設定できます：

- **即時通知**: 重要なイベント（エラー、SRE アラート）
- **バッチ通知**: 日次でまとめて通知
- **通知なし**: 特定イベントの通知を無効化
