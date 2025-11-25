# ops-guides-ja

運用手順書のリポジトリです。

## ドキュメント一覧

| ドキュメント | 説明 |
|-------------|------|
| [ブランチ戦略](./docs/branching-strategy.md) | Git-flow に準拠したブランチ運用ルール |
| [デプロイ戦略](./docs/deployment-strategy.md) | 環境構成とデプロイ・配布のルール |
| [運用手順書（Runbook）](./docs/runbook.md) | 代表的シナリオの step-by-step 手順 |

## 概要

本リポジトリは、開発チームのための運用手順書を管理しています。

### ブランチ戦略

- Git-flow に準拠したブランチ構成
- Semantic Versioning (Sem-Ver) に基づくバージョン管理
- Feature-freeze、Hot-fix、非 Hot-fix 修正の運用ルール
- PR とレビューの原則

### デプロイ戦略

- `develop` ブランチ → DEV 環境（自動/半自動/手動）
- `vX.Y.Z-rc.N` タグ → STG 環境（release/hotfix/master 上にタグ付け可能）
- `vX.Y.Z` タグ → PRD 環境（master 上にのみ存在）
- モバイルアプリの配布（distribute）と審査対応フロー

### 運用手順書（Runbook）

代表的な運用シナリオごとの step-by-step 手順を記載しています。

- 新機能開発
- リリースブランチ作成
- RC タグ作成と STG デプロイ
- 本番リリース
- Hot-fix 適用
- 審査リジェクト対応
- リリース後のバグ修正

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before contributing to this project.