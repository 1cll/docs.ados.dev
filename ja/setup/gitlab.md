# GitLab 連携

ADOS は GitLab.com と GitLab Self-Managed の両方をサポートしています。

## セットアップ

### 1. Personal Access Token を作成

1. GitLab → User Settings → **Access Tokens** に移動
2. **Add new token** をクリック
3. 以下のスコープを選択：
   - `api` — フル API アクセス
   - `read_repository` — リポジトリの読み取り
   - `write_repository` — リポジトリへの書き込み
4. トークンを生成してコピー

### 2. ADOS に登録

1. ダッシュボードの **Settings** → **Connections** に移動
2. **Add New Connection** → **GitLab** を選択
3. 以下を入力：
   - **Connection Name**: 任意の名前（例：`gitlab-production`）
   - **Token**: 生成した PAT
   - **Base URL**: `https://gitlab.com`（Self-Managed の場合はカスタム URL）
4. **Save** をクリック

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

オンプレミスの GitLab もサポートしています：

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
| Issue Created | ラベル付き Issue を自動検知 |
| Merge Request | AI が生成した MR のステータスを追跡 |
| Pipeline | CI/CD パイプラインの監視と自動修復 |

> [!TIP]
> GitLab では「Pull Request」の代わりに「Merge Request」を使用します。ADOS は用語に関わらず同じ機能を提供します。
