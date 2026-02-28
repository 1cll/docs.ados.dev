# GitLab 連携

ADOS は GitLab.com および GitLab Self-Managed に対応しています。

## セットアップ

### 1. Personal Access Token の作成

1. GitLab → User Settings → **Access Tokens**
2. **Add new token** をクリック
3. 以下のスコープを選択：
   - `api` — フル API アクセス
   - `read_repository` — リポジトリの読み取り
   - `write_repository` — リポジトリへの書き込み
4. トークンを生成してコピー

### 2. ADOS に登録

1. ダッシュボードの **Settings** → **Connections** に移動
2. **新しい接続を追加** → **GitLab** を選択
3. 以下を入力：
   - **接続名**: 任意の名前（例: `gitlab-production`）
   - **Token**: 生成した PAT
   - **Base URL**: `https://gitlab.com`（Self-Managed の場合はカスタム URL）
4. **保存**

### 3. リポジトリを登録

```yaml
repos:
  - name: my-gitlab-repo
    owner: my-group
    repo: my-project
    vcs_provider: gitlab
    label: ados
    target_branch: main
```

## GitLab Self-Managed

オンプレミスの GitLab にも対応しています：

```yaml
repos:
  - name: my-repo
    owner: my-group
    repo: my-project
    vcs_provider: gitlab
    vcs_base_url: https://gitlab.my-company.com
```

## 対応イベント

| イベント | 説明 |
|---------|------|
| Issue 作成 | ラベル付き Issue を自動検知 |
| Merge Request | AI が生成した MR のステータス追跡 |
| Pipeline | CI/CD パイプラインの監視・自動修復 |

> [!TIP]
> GitLab では「Pull Request」の代わりに「Merge Request」が使われます。ADOS はどちらの用語でも同じ機能を提供します。
