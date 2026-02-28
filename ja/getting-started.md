# クイックスタート

数ステップで ADOS を始められます。**5 分で初めての AI 自動タスクを体験しましょう。**

## 前提条件

- GitHub / GitLab / Bitbucket アカウント
- 対象リポジトリへの Write 権限

## ステップ 1：アカウント作成

1. [ADOS ダッシュボード](https://ados-platform-dashboard.web.app) にアクセス
2. **Google アカウント** または **GitHub アカウント** でサインアップ
3. 初回ログイン時にオンボーディングウィザードが表示されます

> [!TIP]
> 14 日間の Pro トライアルが自動適用されます。クレジットカードは不要です。

## ステップ 2：GitHub を接続

1. サイドバーの **Settings** → **GitHub Integration** に移動
2. **Install GitHub App** をクリック
3. GitHub 上でインストール先の Organization / リポジトリを選択
4. インストール完了後、ダッシュボードにリダイレクトされます

詳しくは [GitHub 連携](ja/setup/github.md) を参照してください。

## ステップ 3：リポジトリを登録

1. **Settings** → **Repositories** に移動
2. **Add Repository** をクリック
3. 接続済みリポジトリの一覧から対象を選択
4. 以下を設定：

| 設定項目 | 説明 | 推奨値 |
|---------|------|--------|
| **Label** | ADOS が監視する Issue ラベル | `ados` |
| **Target Branch** | PR のベースブランチ | `main` |
| **Default AI Agent** | 使用する AI エージェント | `claude` |
| **Model** | AI モデル | `claude-sonnet-4` |

5. **Save** をクリック

## ステップ 4：Issue を作成

対象リポジトリで Issue を作成し、設定したラベル（例：`ados`）を付けます。

```
Title: ユーザー認証にレート制限を追加

Body:
- /api/v1/auth/login エンドポイントにレート制限を追加
- 同一 IP から 1 分あたり最大 10 回のログイン試行に制限
- 制限超過時は 429 Too Many Requests を返す
- テストも追加すること
```

> [!TIP]
> Issue の説明が具体的であるほど、AI の出力品質は向上します。

## ステップ 5：AI が自動処理

ADOS はラベル付き Issue を自動検知し、以下のフローで処理します：

1. **Issue を解析** — タイトル、本文、コメントを読み取り
2. **リポジトリを理解** — コードベースを分析
3. **コードを実装** — AI エージェントがコードを記述
4. **テストを実行** — 既存のテストが通るか検証
5. **PR を作成** — 実装を Pull Request として提出

ダッシュボードの **Pull Requests** ページで進捗をリアルタイムに確認できます。

## ステップ 6：レビュー & マージ

AI が作成した PR をレビューし、問題なければマージします。

```bash
# CLI でマージ
gh pr merge <PR_NUMBER> --squash
```

## 次のステップ

- [copilot-instructions.md を書く](ja/guides/writing-instructions.md) — AI の出力品質を劇的に改善
- [パイプライン自動修復](ja/guides/pipeline-watcher.md) — CI/CD の失敗を自動修正
- [SRE 運用監視](ja/guides/sre-agent.md) — 本番環境のインシデントを自動検知
- [AutoPilot モード](ja/guides/autopilot.md) — リポジトリを自律的に継続改善

## ダッシュボード概要

ダッシュボードの各ページの詳細は [ダッシュボード概要](ja/dashboard.md) を参照してください。
