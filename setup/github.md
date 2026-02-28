# GitHub 連携

ADOS は GitHub と 2 つの方法で連携できます。**GitHub App（推奨）** と **Personal Access Token (PAT)** です。

## 方法 1: GitHub App（推奨）

GitHub App を使うと、リポジトリ単位で細かい権限管理が可能です。

### インストール手順

1. ダッシュボードの **Settings** → **GitHub 連携** に移動
2. **GitHub App をインストール** をクリック
3. GitHub のインストール画面で：
   - 対象の組織またはユーザーアカウントを選択
   - **すべてのリポジトリ** または **選択したリポジトリ** を指定
4. **Install** をクリック
5. ADOS ダッシュボードに自動リダイレクト

### 必要な権限

GitHub App は以下の権限を要求します：

| 権限 | レベル | 用途 |
|------|--------|------|
| **Issues** | Read & Write | Issue の読み取り・コメント |
| **Pull requests** | Read & Write | PR の作成・更新 |
| **Contents** | Read & Write | コードの読み取り・プッシュ |
| **Actions** | Read | CI/CD ステータスの監視 |
| **Metadata** | Read | リポジトリ情報の取得 |

### Webhook 設定

GitHub App のインストール時に Webhook が自動設定されます。以下のイベントを受信します：

- `issues` — Issue の作成・更新・ラベル変更
- `issue_comment` — Issue コメントの追加
- `pull_request` — PR のオープン・マージ・クローズ
- `workflow_run` — GitHub Actions の実行結果

## 方法 2: Personal Access Token (PAT)

PAT を使えば、GitHub App をインストールせずに連携できます。

### トークンの作成

1. GitHub Settings → Developer settings → **Fine-grained tokens**
2. **Generate new token** をクリック
3. 以下の権限を付与：
   - `repo` (Full control)
   - `workflow` (Update GitHub Action workflows)
4. トークンを生成してコピー

### ADOS に登録

1. ダッシュボードの **Settings** → **GitHub 連携** に移動
2. **Token** タブを選択
3. トークンを貼り付けて **保存**

> [!WARNING]
> PAT は組織全リポジトリにアクセスできるため、GitHub App よりも権限が広くなります。可能であれば GitHub App の使用を推奨します。

## 接続ステータスの確認

**Settings** → **GitHub 連携** ページで接続ステータスを確認できます：

- 🟢 **接続済み** — 正常に連携中
- 🟡 **トークン期限切れ** — トークンの更新が必要
- 🔴 **未接続** — 設定が必要

## GitHub Enterprise Server

GitHub Enterprise Server（オンプレミス）にも対応しています。リポジトリ設定で `vcs_base_url` にカスタム URL を指定してください。

```yaml
repos:
  - name: my-repo
    owner: my-org
    repo: my-repo
    vcs_provider: github
    vcs_base_url: https://github.my-company.com/api/v3
```
