# ADOS — AI DevOps Platform

> **Copilot はコードを書く。ADOS はチームを回す。**

ADOS（AI DevOps as a Service）は、**AI 駆動の開発運用プラットフォーム**です。GitHub Issue から AI コーディングエージェントが自動でコードを実装し、Pull Request を作成。さらに CI/CD の自動修復、SRE 監視、自己改善まで、DevOps チーム全体を AI が 24/7 で回します。

## 主な機能

| 機能 | 説明 |
|------|------|
| 🤖 **マルチ AI エージェント** | Copilot、Claude、Codex を Issue の複雑さに応じて自動選択 |
| 🔄 **パイプライン自動修復** | CI/CD の異常を検知し、自動で修正 PR を作成 |
| 📡 **SRE 運用監視** | エラーレート・レイテンシ・OOM を常時監視 → 自動 Issue 化 |
| 🧠 **AI バックログ & AutoPilot** | AI がリポジトリを分析し改善 Issue を自動生成・実装 |
| 💬 **Agentic Chat** | リポジトリのコンテキストを理解した AI チャット |
| 📊 **ダッシュボード** | ジョブ統計、成功率、トレンドをリアルタイム表示 |
| 🖥️ **セルフホスト Runner** | Docker 環境でオンプレ実行。コードが外部に出ない |
| 🔀 **ワークフロー** | Plan → Approval → Implement → Review の承認ゲート |
| 🔒 **マルチ VCS 対応** | GitHub / GitLab / Bitbucket に対応 |

## このドキュメントについて

このドキュメントでは ADOS の導入から運用までを網羅しています。

- はじめての方 → [クイックスタート](ja/getting-started.md)
- 機能を知りたい方 → [機能ガイド](ja/guides/issue-automation.md)
- セルフホストしたい方 → [Work Runner セットアップ](ja/self-host/work-runner.md)
- 設定を詳しく知りたい方 → [設定リファレンス](ja/reference/config.md)
