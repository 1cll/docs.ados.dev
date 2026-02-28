# GitHub 連携

ADOS は GitHub との接続に **GitHub App（推奨）** と **Personal Access Token（PAT）** の 2 つの方法をサポートしています。

## 方法 1：GitHub App（推奨）

GitHub App を使用すると、リポジトリごとの細かい権限管理が可能です。

### インストール手順

1. ダッシュボードの **Settings** → **GitHub Integration** に移動
2. **Install GitHub App** をクリック
3. GitHub のインストール画面で：
   - 対象の Organization またはユーザーアカウントを選択
   - **All repositories** または **Selected repositories** を選択
4. **Install** をクリック
5. 自動的に ADOS ダッシュボードにリダイレクトされます

### 必要な権限

GitHub App は以下の権限をリクエストします：

| 権限 | レベル | 用途 |
|------|--------|------|
| **Issues** | Read & Write | Issue の読み取りとコメント投稿 |
| **Pull requests** | Read & Write | PR の作成・更新 |
| **Contents** | Read & Write | コードの読み取りと変更プッシュ |
| **Actions** | Read | CI/CD ステータスの監視 |
| **Metadata** | Read | リポジトリ情報の取得 |

### Webhook 設定

Webhook は GitHub App インストール時に自動設定されます。以下のイベントを受信します：

- `issues` — Issue の作成、更新、ラベル変更
- `issue_comment` — Issue コメントの追加
- `pull_request` — PR のオープン、マージ、クローズ
- `workflow_run` — GitHub Actions の実行結果

## 方法 2：Personal Access Token（PAT）

GitHub App をインストールせずに PAT で接続する方法です。

### トークンの作成

1. GitHub Settings → Developer settings → **Fine-grained tokens** に移動
2. **Generate new token** をクリック
3. 以下の権限を付与：
   - `repo`（Full control）
   - `workflow`（Update GitHub Action workflows）
4. トークンを生成してコピー

### ADOS に登録

1. ダッシュボードの **Settings** → **GitHub Integration** に移動
2. **Token** タブを選択
3. トークンを貼り付けて **Save** をクリック

> [!WARNING]
> PAT は Organization 内のすべてのリポジトリにアクセスできるため、GitHub App よりスコープが広くなります。可能な限り GitHub App の使用を推奨します。

## 接続ステータスの確認

**Settings** → **GitHub Integration** ページで接続ステータスを確認できます：

- 🟢 **Connected** — 連携が正常に動作中
- 🟡 **Token Expired** — トークンの更新が必要
- 🔴 **Not Connected** — セットアップが必要

## GitHub Enterprise Server

GitHub Enterprise Server（オンプレミス）もサポートしています。リポジトリ設定の `vcs_base_url` でカスタム URL を指定します。

```yaml
repos:
  - name: my-repo
    owner: my-org
    repo: my-repo
    vcs_provider: github
    vcs_base_url: https://github.my-company.com/api/v3
```
