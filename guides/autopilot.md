# AutoPilot モード

AutoPilot は ADOS の全自動運転モードです。Issue の検知から実装、PR 作成まで人手を介さず連続的に処理します。

## 概要

通常モードでは Issue を 1 件ずつ処理しますが、AutoPilot モードでは：

- リポジトリ内の未処理 Issue を **自動的にキューイング**
- **並行処理** で複数 Issue を同時に対応
- **優先度フィルタ** で重要な Issue を優先
- **レート制限** で安全に運用

## 有効化

```yaml
repos:
  - name: my-repo
    workers:
      autopilot:
        enabled: true
        min_open_issues: 1          # キューに最低1件あれば起動
        max_per_cycle: 3            # 1 サイクルの最大処理数
        max_per_day: 20             # 1 日の最大処理数
        check_interval: 10m         # チェック間隔
        cooldown: 30m               # サイクル間のクールダウン
        priority_filter:            # 処理する優先度
          - high
          - medium
        category_filter:            # 処理するカテゴリ
          - bug
          - feature
        require_approval: false     # PR に承認を求めるか
```

## パラメータ詳細

| パラメータ | 型 | デフォルト | 説明 |
|-----------|---|-----------|------|
| `enabled` | bool | `false` | AutoPilot の有効/無効 |
| `min_open_issues` | int | `1` | 起動に必要な最小 Issue 数 |
| `max_per_cycle` | int | `5` | 1 サイクルで処理する最大数 |
| `max_per_day` | int | `50` | 1 日の処理上限 |
| `check_interval` | duration | `10m` | Issue チェックの間隔 |
| `cooldown` | duration | `30m` | サイクル間の待機時間 |
| `priority_filter` | []string | `["*"]` | 処理する優先度の一覧 |
| `category_filter` | []string | `["*"]` | 処理するカテゴリの一覧 |
| `require_approval` | bool | `false` | マージ前に承認を要求 |
| `model` | string | `""` | 使用するモデル（空=自動選択） |
| `focus` | []string | `[]` | バックログのフォーカスエリア |

## AutoPilot + AI バックログ

AutoPilot と [AI バックログ生成](guides/ai-backlog.md) を組み合わせると、完全自動の改善サイクルが実現します：

```
┌────────────────┐     ┌──────────────┐     ┌────────────────┐
│  AI バックログ    │ ──▶ │  Issue 生成   │ ──▶ │  AutoPilot     │
│  (分析・提案)    │     │  (自動作成)   │     │  (自動実装)    │
└────────────────┘     └──────────────┘     └────────────────┘
         ▲                                          │
         └──────────────────────────────────────────┘
                     継続的改善ループ
```

## 安全機構

### レート制限
- `max_per_cycle` と `max_per_day` で処理量を制限
- API レート制限にも自動対応

### クールダウン
- 連続処理によるリソース消費を抑制
- サイクル間に `cooldown` 分の待機時間

### 承認ゲート
- `require_approval: true` を設定すると、PR が作成された後も人間の承認なしにはマージされません
- チームのコードレビュープロセスを維持

### ロック機構
- 同じ Issue が複数回処理されることを防止
- 分散ロックで安全な並行処理

## ユースケース

### 技術的負債の解消
1. AI バックログで改善点を洗い出し
2. AutoPilot が Issue を自動処理
3. チームは PR のレビューに集中
4. 週末に放置していたら翌週月曜には負債が半減

### 新プロジェクトの立ち上げ
1. テンプレートリポジトリを作成
2. 必要な機能を Issue として大量登録
3. AutoPilot が順次実装
4. 基盤コードが数時間で完成

> [!WARNING]
> AutoPilot は強力ですが、`require_approval: true` の設定を推奨します。AI の変更を人間がレビューすることで品質を担保できます。
