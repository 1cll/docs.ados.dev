# ADOS — AI DevOps Platform

> **Copilot はコードを書く。ADOS はチームを回す。**

ADOS（AI DevOps as a Service）は、**AI 駆動の開発運用プラットフォーム**です。GitHub Issue から AI コーディングエージェントが自動でコードを実装し、Pull Request を作成。さらに CI/CD の自動修復、SRE 監視、自律改善まで、DevOps チーム全体を AI が 24/7 で回します。

## 主な機能

| 機能 | 説明 |
|------|------|
| 🤖 **マルチ AI エージェント** | Copilot、Claude、Codex を Issue の複雑さに応じて自動選択 |
| 🔄 **パイプライン自動修復** | CI/CD の失敗を検知し、修復 PR を自動生成 |
| 📡 **SRE 運用監視** | エラー率・レイテンシ・OOM を常時監視 → 自動 Issue 化 |
| 🧠 **AI バックログ & AutoPilot** | AI がリポジトリを分析し改善 Issue を自動生成・実装 |
| 💬 **Agentic Chat** | リポジトリのコンテキストを理解した AI チャット |
| 📊 **ダッシュボード** | ジョブ統計、成功率、トレンドをリアルタイム表示 |
| 🖥️ **セルフホスト Runner** | Docker 環境でオンプレ実行。コードが外部に出ない |
| 🔀 **ワークフロー** | Plan → Approval → Implement → Review の承認ゲート |
| 🔒 **マルチ VCS 対応** | GitHub / GitLab / Bitbucket に対応 |

## アーキテクチャ概要

```
┌─────────────────────────────────┐
│  Web UI (React SPA)             │
│  ├── ダッシュボード              │
│  ├── Agentic Chat               │
│  ├── ワークフローエディタ        │
│  └── 設定・連携管理              │
└───────────┬─────────────────────┘
            │ /api/v1/*
            ▼
┌─────────────────────────────────┐
│  API Server (Cloud Run)         │
│  ├── REST API                   │
│  ├── WebSocket (Runner 接続)     │
│  ├── Webhook (GitHub/GitLab)     │
│  └── Firebase Auth              │
└───┬───────────────┬─────────────┘
    │               │ WebSocket
    ▼               ▼
┌──────────┐  ┌──────────────────┐
│ Firestore│  │ Work Runner      │
│ (State)  │  │ (Docker コンテナ)  │
│          │  │ ├── Copilot CLI   │
│          │  │ ├── Claude CLI    │
│          │  │ └── Codex CLI     │
└──────────┘  └──────────────────┘
```

## 技術スタック

- **バックエンド**: Go 1.24+
- **フロントエンド**: React + TypeScript (Vite)
- **インフラ**: Google Cloud Run, Firebase Hosting, Firestore
- **AI エージェント**: GitHub Copilot CLI, Claude Code, OpenAI Codex
- **認証**: Firebase Auth (Google OAuth + GitHub OAuth)

## このドキュメントについて

このドキュメントでは ADOS の導入から運用までを網羅しています。

- **はじめての方** → [クイックスタート](getting-started.md)
- **機能を知りたい方** → [機能ガイド](guides/issue-automation.md)
- **セルフホストしたい方** → [Work Runner セットアップ](self-host/work-runner.md)
- **設定を詳しく知りたい方** → [設定リファレンス](reference/config.md)
