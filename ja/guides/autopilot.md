# AutoPilot モード

AutoPilot は ADOS の完全自動モードです。Issue の検知から実装、PR 作成まで人手を介さず継続的に処理します。

## 概要

通常モードが Issue を 1 件ずつ処理するのに対し、AutoPilot モードは：

- リポジトリの未処理 Issue を**自動でキューイング**
- **複数 Issue を並列処理**
- 優先度フィルタで**重要な Issue を優先**
- レート制限で**安全に運用**

## AutoPilot の有効化

```yaml
repos:
  - name: my-repo
    workers:
      autopilot:
        enabled: true
        min_open_issues: 1          # 最低 1 件の Issue でスタート
        max_per_cycle: 3            # 1 サイクルあたり最大 3 件
        max_per_day: 20             # 1 日あたり最大 20 件
        check_interval: 10m         # チェック間隔
        cooldown: 30m               # サイクル間のクールダウン
        priority_filter:            # 処理する優先度
          - high
          - medium
        category_filter:            # 処理するカテゴリ
          - bug
          - feature
        require_approval: false     # マージ前に承認を要求
```

## パラメータ詳細

| パラメータ | 型 | デフォルト | 説明 |
|-----------|------|---------|------|
| `enabled` | bool | `false` | AutoPilot の有効/無効 |
| `min_open_issues` | int | `1` | スタートに必要な最低 Issue 数 |
| `max_per_cycle` | int | `5` | 1 サイクルで処理する最大件数 |
| `max_per_day` | int | `50` | 1 日で処理する最大件数 |
| `check_interval` | duration | `10m` | Issue チェック間隔 |
| `cooldown` | duration | `30m` | サイクル間の待機時間 |
| `priority_filter` | []string | `["*"]` | 処理する優先度 |
| `category_filter` | []string | `["*"]` | 処理するカテゴリ |
| `require_approval` | bool | `false` | マージ前に承認を要求 |
| `model` | string | `""` | 使用するモデル（空 = 自動選択） |
| `focus` | []string | `[]` | バックログのフォーカス領域 |

## AutoPilot + AI バックログ

AutoPilot を [AI バックログ生成](ja/guides/ai-backlog.md) と組み合わせると、完全自動の改善サイクルが実現します：

```
┌────────────────┐     ┌──────────────┐     ┌────────────────┐
│  AI バックログ   │ ──▶ │  Issue        │ ──▶ │  AutoPilot     │
│  （分析＆       │     │  作成         │     │  （自動         │
│   提案）        │     │  （自動）      │     │   実装）        │
└────────────────┘     └──────────────┘     └────────────────┘
         ▲                                          │
         └──────────────────────────────────────────┘
                    継続的改善ループ
```

## 安全機構

### レート制限
- `max_per_cycle` と `max_per_day` で処理量を制御
- API レート制限の自動ハンドリング

### クールダウン
- 連続処理による過剰なリソース消費を防止
- `cooldown` でサイクル間の待機時間を設定

### 承認ゲート
- `require_approval: true` 設定時、人の承認なしに PR はマージされない
- チームのコードレビュープロセスを維持

### 重複防止
- 同じ Issue が複数回処理されることを防止

## ユースケース

### 技術的負債の返済
1. AI バックログが改善箇所を特定
2. AutoPilot が Issue を自動処理
3. チームは PR レビューに集中
4. 週末に稼働させておけば、月曜には技術的負債が半減

### 新規プロジェクトのブートストラップ
1. テンプレートリポジトリを作成
2. 必要な機能を Issue として一括作成
3. AutoPilot が順次実装
4. 数時間でベースコードが完成

> [!WARNING]
> AutoPilot は強力ですが、`require_approval: true` の設定を推奨します。AI の変更を人がレビューすることで品質を担保できます。
