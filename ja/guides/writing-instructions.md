# copilot-instructions.md の書き方

`copilot-instructions.md` はリポジトリ固有のルールやコンテキストを AI エージェントに伝えるファイルです。ADOS はこのファイルを読み取り、AI の実装品質を大幅に向上させます。

## ファイルの場所

```
.github/copilot-instructions.md
```

リポジトリルートの `.github/` ディレクトリに配置します。

## 基本構成

```markdown
# プロジェクト概要
このリポジトリは ○○ のバックエンド API です。

# 技術スタック
- 言語: TypeScript
- フレームワーク: Express.js
- データベース: PostgreSQL + Prisma
- テスト: Jest

# コーディング規約
- 変数名は camelCase を使用
- 関数はアロー関数で記述
- エラーハンドリングは必ず try-catch を使用

# ファイル構成
- `src/controllers/` — API コントローラー
- `src/services/` — ビジネスロジック
- `src/models/` — Prisma モデル
- `src/middleware/` — Express ミドルウェア

# テストルール
- すべてのサービス関数にユニットテストを記述
- テストファイルは `__tests__/` ディレクトリに配置
- 説明的なテスト名を記述

# 禁止パターン
- `any` 型は使用しない
- `console.log` は使用しない（logger を使用）
- 生 SQL クエリは使用しない（Prisma を使用）
```

## 効果的な記述のコツ

### 1. 具体的に書く

❌ 悪い例：
```markdown
きれいなコードを書いて
```

✅ 良い例：
```markdown
- 関数は 30 行以内に収める
- ネストは最大 3 段階
- 早期リターンパターンを使用する
```

### 2. アーキテクチャを記述

```markdown
# アーキテクチャ
- クリーンアーキテクチャを採用
- レイヤー構成: Controller → UseCase → Repository
- 依存の方向: 外側 → 内側
- DI コンテナ: tsyringe
```

### 3. 既存のパターンを示す

```markdown
# 新しい API エンドポイントの追加方法
1. `src/controllers/` に新しいコントローラーを作成
2. 対応するサービスを `src/services/` に作成
3. `src/routes.ts` にルーティングを追加
4. `src/__tests__/` にテストを作成

参考: `src/controllers/users.controller.ts`
```

### 4. 禁止パターンを列挙

```markdown
# やってはいけないこと
- グローバル変数
- 同期的ファイル操作（例: fs.readFileSync）
- 環境変数への直接アクセス（config モジュールを使用）
- マジックナンバー
```

### 5. 言語/フレームワーク固有のルール

```markdown
# React ルール
- クラスコンポーネントは使用しない（Functional Components のみ）
- 状態管理は useState / useReducer を使用
- グローバルステートは Zustand を使用
- Tailwind CSS のユーティリティクラスを使用

# API レスポンスフォーマット
すべての API は以下のフォーマットでレスポンスを返す：
{
  "success": true,
  "data": { ... },
  "error": null
}
```

## ADOS が参照するその他のファイル

| ファイル | 用途 |
|---------|------|
| `.github/copilot-instructions.md` | メインの指示ファイル |
| `.ados.yaml` | ADOS 固有の設定 |
| `README.md` | プロジェクト概要として参照 |
| `CONTRIBUTING.md` | コントリビューションガイドライン |

> [!TIP]
> `copilot-instructions.md` の品質は AI の実装品質に直結します。新しいチームメンバーへのオンボーディングドキュメントと同じレベルの詳細さを目指しましょう。
