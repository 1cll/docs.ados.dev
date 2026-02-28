# copilot-instructions.md の書き方

`copilot-instructions.md` は、AI エージェントにリポジトリ固有のルールやコンテキストを伝えるためのファイルです。ADOS はこのファイルを読み取り、AI の実装品質を大幅に向上させます。

## 配置場所

```
.github/copilot-instructions.md
```

リポジトリのルートにある `.github/` ディレクトリ内に配置します。

## 基本構成

```markdown
# プロジェクト概要
このリポジトリは〇〇のバックエンド API です。

# 技術スタック
- 言語: TypeScript
- フレームワーク: Express.js
- データベース: PostgreSQL + Prisma
- テスト: Jest

# コーディング規約
- 変数名はキャメルケースを使用
- 関数はアロー関数で記述
- エラーハンドリングは必ず try-catch で行う

# ファイル構造
- `src/controllers/` — API コントローラー
- `src/services/` — ビジネスロジック
- `src/models/` — Prisma モデル
- `src/middleware/` — Express ミドルウェア

# テストルール
- すべてのサービス関数にユニットテストを書く
- テストファイルは `__tests__/` ディレクトリに配置
- テスト名は日本語で記述

# 禁止事項
- `any` 型の使用禁止
- `console.log` の使用禁止（ロガーを使用）
- 直接 SQL クエリの使用禁止（Prisma を使用）
```

## 効果的な書き方のポイント

### 1. 具体的に書く

❌ 悪い例:
```markdown
きれいなコードを書いてください
```

✅ 良い例:
```markdown
- 関数は 30 行以内に収める
- ネストは 3 レベルまで
- 早期リターンパターンを使用する
```

### 2. アーキテクチャを明記

```markdown
# アーキテクチャ
- Clean Architecture を採用
- レイヤー: Controller → UseCase → Repository
- 依存性の方向: 外側 → 内側
- DI コンテナ: tsyringe を使用
```

### 3. 既存のパターンを示す

```markdown
# 新しい API エンドポイントの追加方法
1. `src/controllers/` に新しいコントローラーを作成
2. `src/services/` に対応するサービスを作成
3. `src/routes.ts` にルーティングを追加
4. `src/__tests__/` にテストを作成

参考: `src/controllers/users.controller.ts`
```

### 4. NG パターンを明記

```markdown
# やってはいけないこと
- グローバル変数の使用
- 同期的なファイル操作（fs.readFileSync 等）
- 環境変数の直接参照（config モジュールを経由）
- マジックナンバーの使用
```

### 5. 言語・フレームワーク固有のルール

```markdown
# React ルール
- クラスコンポーネントは使用しない（Functional Component のみ）
- 状態管理は useState / useReducer を使用
- グローバル状態は Zustand を使用
- CSS は Tailwind CSS のユーティリティクラスを使用

# API レスポンス形式
すべての API は以下の形式で返す：
{
  "success": true,
  "data": { ... },
  "error": null
}
```

## ADOS が参照する他のファイル

| ファイル | 用途 |
|---------|------|
| `.github/copilot-instructions.md` | メインの指示ファイル |
| `.ados.yaml` | ADOS 固有の設定 |
| `README.md` | プロジェクト概要として参照 |
| `CONTRIBUTING.md` | コントリビューションガイド |

> [!TIP]
> `copilot-instructions.md` の品質は AI の実装品質に直結します。新しいメンバーへのオンボーディング資料と同じレベルの情報量を目指してください。
