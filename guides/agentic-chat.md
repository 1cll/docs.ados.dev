# Agentic Chat

Agentic Chat は、リポジトリのコンテキストを理解した AI とリアルタイムに対話できる機能です。

## 概要

通常の AI チャットとは異なり、Agentic Chat は：

- リポジトリのコード構造を理解
- ファイルの読み取り・編集が可能
- ターミナルコマンドを実行可能
- 変更内容をそのまま PR 化可能

## 使い方

### ダッシュボードから

1. ダッシュボードで対象リポジトリを選択
2. チャットアイコンをクリック
3. 自然言語で依頼を入力

### CLI から

```bash
# Agentic Chat モードで起動
export ADOS_MODE=chat
ados start
```

## 対話の例

### コードの質問

```
ユーザー: このリポジトリの認証の仕組みを教えて
AI: このプロジェクトでは JWT ベースの認証を使っています。
    主要なファイルは...
```

### コード変更の依頼

```
ユーザー: ログインAPIにレートリミットを追加して
AI: 了解しました。以下の変更を行います：
    1. rate-limiter パッケージを追加
    2. /api/login にミドルウェアを設定
    3. テストを追加
    
    [変更をPR化する] ボタンで PR を作成できます。
```

### デバッグ支援

```
ユーザー: POST /api/orders で 500 エラーが出る。原因を調べて
AI: orders.controller.ts を確認しました。
    L45 で items が undefined の場合の処理が漏れています。
    修正コードを提案します...
```

## モード

Agentic Chat にはいくつかの動作モードがあります：

| モード | 環境変数 | 説明 |
|--------|---------|------|
| `chat` | `ADOS_MODE=chat` | チャットのみ |
| `issue` | `ADOS_MODE=issue` | Issue 処理のみ |
| `all` | `ADOS_MODE=all` | チャット + Issue 処理 |

## WebSocket 接続

Agentic Chat はリアルタイム通信に WebSocket を使用しています：

```
wss://api.ados.dev/api/work/runners/ws
```

Work Runner がクラウドまたはセルフホスト環境で WebSocket 接続を維持し、チャットメッセージをリアルタイムに処理します。

## セキュリティ

- チャットの内容はサーバーに保存されません
- リポジトリへのアクセスはユーザーの権限に基づきます
- AI への指示は `copilot-instructions.md` で制御可能

> [!TIP]
> Agentic Chat は「Issue にするほどではないけど、AI に手伝ってほしい」という場面で活用できます。ちょっとした質問やクイックな修正依頼に最適です。
