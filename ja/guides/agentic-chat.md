# Agentic Chat

Agentic Chat は、リポジトリのコンテキストを理解した AI とリアルタイムで対話できる機能です。

## 概要

一般的な AI チャットと異なり、Agentic Chat は：

- リポジトリのコード構造を理解
- ファイルの読み書きが可能
- ターミナルコマンドの実行が可能
- 変更をそのまま PR にできる

## 使い方

### ダッシュボードから

1. ダッシュボードで対象リポジトリを選択
2. チャットアイコンをクリック
3. 自然言語でリクエストを入力

### セルフホスト Work Runner から

Agentic Chat は Work Runner が `chat` または `all` モードで動作している場合に利用できます：

```bash
# Work Runner の環境変数で ADOS_MODE=chat または ADOS_MODE=all を設定
ADOS_MODE=chat
```

詳細は [Work Runner セットアップ](ja/self-host/work-runner.md) を参照してください。

## 会話例

### コードに関する質問

```
ユーザー: このリポジトリの認証はどうなっていますか？
AI: このプロジェクトは JWT ベースの認証を使用しています。
    主要なファイルは...
```

### コード変更の依頼

```
ユーザー: ログイン API にレート制限を追加して
AI: 承知しました。以下の変更を行います：
    1. rate-limiter パッケージを追加
    2. /api/login にミドルウェアを設定
    3. テストを追加
    
    [PR を作成] をクリックして Pull Request を作成できます。
```

### デバッグ支援

```
ユーザー: POST /api/orders で 500 エラーが出ます。調査してもらえますか？
AI: orders.controller.ts を確認しました。
    L45 で items が undefined の場合のハンドラーが不足しています。
    修正案はこちらです...
```

## モード

Agentic Chat は複数の動作モードに対応しています：

| モード | 環境変数 | 説明 |
|--------|---------|------|
| `chat` | `ADOS_MODE=chat` | チャットのみ |
| `issue` | `ADOS_MODE=issue` | Issue 処理のみ |
| `all` | `ADOS_MODE=all` | チャット + Issue 処理 |

## セキュリティ

- チャット内容はサーバーに保存されない
- リポジトリへのアクセスはユーザーの権限に基づく
- `copilot-instructions.md` で AI の指示を制御可能

> [!TIP]
> Agentic Chat は、Issue を作るほどではないが AI の手を借りたい場面に最適です。ちょっとした質問や小さな修正依頼に便利です。
