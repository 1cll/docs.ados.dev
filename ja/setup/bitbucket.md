# Bitbucket 連携

ADOS は Bitbucket Cloud をサポートしています。

## セットアップ

### 1. App Password を作成

1. Bitbucket → Personal settings → **App passwords** に移動
2. **Create app password** をクリック
3. 以下の権限を選択：
   - `Repositories: Read, Write, Admin`
   - `Pull requests: Read, Write`
   - `Issues: Read, Write`
4. パスワードを生成してコピー

### 2. ADOS に登録

1. ダッシュボードの **Settings** → **Connections** に移動
2. **Add New Connection** → **Bitbucket** を選択
3. 以下を入力：
   - **Connection Name**: 任意の名前（例：`bitbucket-main`）
   - **Username**: Bitbucket のユーザー名
   - **App Password**: 生成したパスワード
4. **Save** をクリック

### 3. リポジトリを登録

```yaml
repos:
  - name: my-bb-repo
    owner: my-workspace
    repo: my-repo
    vcs_provider: bitbucket
    label: ados
    target_branch: main
```

## Bitbucket Server

Bitbucket Server（オンプレミス）のサポートは今後のリリースで予定しています。

> [!TIP]
> Bitbucket Cloud のワークスペース ID は Settings → Workspace settings で確認できます。
