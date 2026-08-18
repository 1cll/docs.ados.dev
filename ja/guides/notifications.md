# 通知設定

ADOS は処理の進捗や結果を Slack、Discord、Email、Signal で通知できます。

## 対応チャンネル

| チャンネル | 設定方法 |
|-----------|---------|
| **Slack** | Webhook URL |
| **Discord** | Webhook URL |
| **Email** | メールアドレス |
| **Signal** | QR コードリンク（signal-cli-rest-api 経由） |

## Slack 連携

### 1. Incoming Webhook を作成

1. [Slack API](https://api.slack.com/apps) で新しいアプリを作成
2. **Incoming Webhooks** を有効化
3. **Add New Webhook to Workspace** でチャンネルを選択
4. Webhook URL をコピー

### 2. ADOS に登録

1. ダッシュボードの **Settings** → **Notifications** に移動
2. **Slack** タブを選択
3. Webhook URL を貼り付け
4. **Send Test Notification** で動作確認
5. **Save** をクリック

### 通知例

```
🚀 ADOS | Issue #42 を処理中
リポジトリ: my-org/my-repo
タイトル: ログイン API にレート制限を追加
エージェント: Claude (claude-sonnet-4)
ステータス: 実装中...
```

```
✅ ADOS | PR 作成完了
リポジトリ: my-org/my-repo
PR #123: ログイン API にレート制限を追加
変更ファイル: 3 ファイル (+45 -12)
レビュー → https://github.com/my-org/my-repo/pull/123
```

## Discord 連携

### 1. Webhook を作成

1. Discord サーバー設定 → **連携サービス** → **ウェブフック**
2. **新しいウェブフック** をクリック
3. チャンネルを選択
4. **ウェブフック URL をコピー**

### 2. ADOS に登録

1. ダッシュボードの **Settings** → **Notifications** に移動
2. **Discord** タブを選択
3. Webhook URL を貼り付け
4. **Save** をクリック

## Email 通知

1. ダッシュボードの **Settings** → **Notifications** に移動
2. **Email** タブを選択
3. 通知先メールアドレスを追加
4. **Save** をクリック

## Signal 連携

Signal 通知は [signal-cli-rest-api](https://github.com/bbernhard/signal-cli-rest-api) をオーケストレーター上の Docker サイドカーとして使用します。API URL や電話番号を手動設定する代わりに、ワンクリック QR コードリンクフローを提供します。

### セットアップ

1. ダッシュボードの **Settings** → **Notifications** に移動
2. **Signal** タブを選択
3. Signal コンテナをホストするオーケストレーターを選択
4. **Start Container** をクリック — 選択したオーケストレーター上で signal-cli-rest-api コンテナが起動
5. 画面に QR コードが表示されるので、スマートフォンの Signal アプリでスキャンしてデバイスをリンク
6. リンクが完了するとステータスが **Connected** に変わる
7. **Send Test Notification** で動作確認
8. **Save** をクリック

### アーキテクチャ

- signal-cli-rest-api は内部 Docker ネットワーク（`ados-signal-net`）上で動作し、ホストポートは公開しない
- 通信経路: ブラウザ --> Cloud Run API --> Firestore コマンドキュー --> オーケストレーターポーリング --> signal-cli コンテナ
- 永続ボリューム（`ados-signal-data`）でリンクデータをコンテナ再起動間で保持
- コンテナが 60 日以上経過すると古いコンテナアラート（`EventSignalContainerStale`）が発火

### API エンドポイント

| エンドポイント | メソッド | 説明 |
|---------------|---------|------|
| `/api/v1/signal/qrcode` | GET | デバイスリンク用 QR コードを取得 |
| `/api/v1/signal/status` | GET | Signal コンテナのステータスを取得 |
| `/api/v1/signal/start` | POST | Signal コンテナを起動 |
| `/api/v1/signal/stop` | POST | Signal コンテナを停止 |
| `/api/v1/signal/send-test` | POST | テスト通知を送信 |

---

## インシデント通知

ジョブ進捗通知に加えて、ADOS はクリティカルなシステム障害が発生した際にワンタイムのインシデント通知を送信できます。これはジョブごとの通知ではなく、運用アラート向けの機能です。

### 対応インシデントタイプ

| イベント | 説明 |
|---------|------|
| **GitHub OAuth トークン期限切れ** | トークンのリフレッシュに失敗 |
| **Claude MAX トークン期限切れ** | リフレッシュ時に `invalid_grant` エラー |
| **オーケストレーターオフライン** | オーケストレーターがレポートを停止 |
| **Runner グループエラー** | Runner グループのリコンシリエーションに失敗 |

### チャンネル

インシデント通知は以下のチャンネルで送信できます：

- **Email** — SMTP または Firebase Firestore mail コレクション経由
- **Webhook** — HTTP POST（Slack / Discord 互換）
- **Signal** — signal-cli-rest-api 経由

### 重複排除

- 各インシデントイベントは解決されるまで 1 回のみ通知（スパム防止）
- 根本原因が解決された場合（例：トークンリフレッシュ成功）、インシデントは自動クリアされ、問題が再発した場合に再通知が可能

### 設定方法

1. ダッシュボードの **Settings** → **Incident Notifications** に移動
2. 個々のイベントタイプのオン/オフを切り替え
3. 少なくとも 1 つのチャンネル（Email、Webhook、Signal）を設定
4. **Send Test Notification** で動作確認
5. **Save** をクリック

### 環境変数（SMTP 経由の Email）

| 変数 | 説明 |
|------|------|
| `SMTP_HOST` | SMTP サーバーのホスト名 |
| `SMTP_PORT` | SMTP サーバーのポート |
| `SMTP_USERNAME` | SMTP ユーザー名 |
| `SMTP_PASSWORD` | SMTP パスワード |
| `SMTP_FROM` | 送信元メールアドレス |

> [!TIP]
> Firebase の「Trigger Email from Firestore」エクステンションが設定されている場合、ADOS はそれを主要な Email 配信方法として使用し、Firestore 書き込みが失敗した場合のみ直接 SMTP にフォールバックします。

---

## 通知イベント

どのイベントで通知するかをカスタマイズできます：

| イベント | デフォルト | 説明 |
|---------|----------|------|
| Issue 処理開始 | ✅ | AI が Issue の処理を開始 |
| PR 作成 | ✅ | PR が作成された |
| CI 成功 | ❌ | CI/CD が成功 |
| CI 失敗 | ✅ | CI/CD が失敗 |
| 自動修復 | ✅ | Pipeline Watcher が修復を実施 |
| SRE アラート | ✅ | SRE エージェントがアラートを発報 |
| バックログ生成完了 | ❌ | AI バックログ生成が完了 |
| エラー | ✅ | 処理中にエラーが発生 |

## 通知頻度の制御

通知でチャンネルが溢れるのを防止できます：

- **即時通知**: クリティカルなイベント（エラー、SRE アラート）
- **バッチ通知**: 日次ダイジェスト
- **通知なし**: 特定イベントの通知を無効化
