# ブランチ戦略

本ドキュメントは、Git-flow に準拠したブランチ戦略を定めたものです。

> [!NOTE]
> step-by-step 手順については [ブランチ・デプロイ運用手順書](./branching-and-deployment-runbook.md) を参照してください。

## 目次

1. [ブランチ構成](#ブランチ構成)
2. [ブランチ命名規則](#ブランチ命名規則)
3. [機能開発フロー](#機能開発フロー)
4. [リリースブランチの運用](#リリースブランチの運用)
5. [Hot-fix の運用](#hot-fix-の運用)
6. [非 Hot-fix 修正の運用](#非-hot-fix-修正の運用)
7. [GitHub 設定変更などの特殊な master PR 運用](#github-設定変更などの特殊な-master-pr-運用)
8. [PR とレビューの原則](#pr-とレビューの原則)

---

## ブランチ構成

Git-flow に準拠し、以下のブランチを使用します。

| ブランチ種別 | ブランチ名 | 説明 |
|------------|-----------|-----|
| メインブランチ | `master` | 本番リリース用。vX.Y.Z タグはこのブランチ上にのみ存在する |
| 開発ブランチ | `develop` | 次期リリースの開発用。すべての feature コミットはここにマージされる |
| 機能ブランチ | `feature/*` | 新機能開発用 |
| 修正ブランチ | `fix/*` | バグ修正用 (軽微な修正を含む) |
| 雑務ブランチ | `chore/*` | ドキュメント更新、依存関係更新など機能変更を伴わない作業用 |
| リリースブランチ | `release/vX.Y.Z` | リリース準備用 |
| Hot-fix ブランチ | `hotfix/*` | 緊急修正用 |

---

## ブランチ命名規則

### Semantic Versioning (Sem-Ver)

バージョン番号は Semantic Versioning に従います。

```
vX.Y.Z
```

- **X (Major)**: 後方互換性のない変更
- **Y (Minor)**: 後方互換性のある機能追加
- **Z (Patch)**: 後方互換性のあるバグ修正

### ブランチ名の例

- `feature/add-user-authentication`
- `feature/improve-performance`
- `fix/resolve-login-error`
- `fix/correct-calculation-logic`
- `chore/update-dependencies`
- `chore/update-documentation`
- `release/v1.2.0`
- `release/v2.0.0`
- `hotfix/fix-critical-security-issue`
- `hotfix/fix-payment-error`

---

## 機能開発フロー

機能開発は以下のフローで行います。

1. `develop` ブランチから `feature/*` ブランチを作成
2. 機能を実装し、コミット
3. `develop` ブランチに対する PR を作成
4. レビュー承認後、マージ

> [!IMPORTANT]
> 機能開発 (`feat:` プレフィックスを持つコミット) は `feature` ブランチで行い、PR を通じて `develop` ブランチにマージする必要があります。`master` や `release` ブランチに直接 feature コミットをプッシュしないでください。

📘 **手順の詳細**: [ブランチ・デプロイ運用手順書 - 新機能を開発する](./branching-and-deployment-runbook.md#新機能を開発する)

---

## リリースブランチの運用

### リリースブランチの作成タイミング

- 次のリリースに含める機能が `develop` ブランチに揃った時点
- リリーススケジュールに基づいて計画的に作成
- 定期的なリリースを行うプロジェクトで、Feature-Freeze 時点で新機能がない場合は、パッチバージョンを上げて `release/vX.Y.{Z+1}` として Feature-Freeze を開始

### Feature-Freeze 状態

リリースブランチが作成されると、**Feature-Freeze** 状態となります。

#### Feature-Freeze 状態とは

- `release/vX.Y.Z` ブランチでは新機能の追加は禁止
- 許可される変更:
  - バグ修正
  - ドキュメント修正
  - リリースに必要な設定変更
- 新機能は次のリリースサイクルで `develop` ブランチから開発を継続

📘 **手順の詳細**: [ブランチ・デプロイ運用手順書 - リリースブランチを作成する](./branching-and-deployment-runbook.md#リリースブランチを作成する)

---

## Hot-fix の運用

### Hot-fix とは

本番環境で発生した緊急の問題を修正するための変更です。

### 原則

- **master ブランチに対する PR として修正を作成**
- レビューを受け、承認後にマージ
- Hot-fix は `master` へのマージ後、`develop` にも反映する必要がある

📘 **手順の詳細**: [ブランチ・デプロイ運用手順書 - Hot-fix を適用する](./branching-and-deployment-runbook.md#hot-fix-を適用する)

---

## 非 Hot-fix 修正の運用

### 非 Hot-fix 修正とは

- 機能追加を含まないバグ修正や改善
- 緊急性のない修正
- リファクタリング

### 運用方法

非 Hot-fix 修正の対応方法は、修正が現在の release ブランチに関係するかどうかで決まります。

#### 現在の release ブランチに関係する修正の場合

現在の `release` ブランチ (例: `release/v1.2.0`) に対して修正を適用します。

- 既存の release ブランチに対してバグ修正のみを適用
- 機能追加は禁止

📘 **手順の詳細**: [ブランチ・デプロイ運用手順書 - リリースブランチでのバグ修正 (非 Hot-fix)](./branching-and-deployment-runbook.md#リリースブランチでのバグ修正-非-hot-fix)

#### 現在の release ブランチに関係しない修正の場合

Feature-Freeze 期間中であっても、`develop` ブランチに対して修正を行います。

- `fix/*` または `chore/*` ブランチを `develop` から作成
- 修正を実施し、`develop` ブランチに対する PR を作成
- 修正は次回のリリースに含まれる

📘 **手順の詳細**: [ブランチ・デプロイ運用手順書 - develop ブランチでのバグ修正](./branching-and-deployment-runbook.md#develop-ブランチでのバグ修正)

#### Feature-Freeze 中ではない場合

Feature-Freeze 中ではない場合 (つまり `release/vX.Y.Z` ブランチが存在しない場合) は、すべての非 Hot-fix 修正は `develop` ブランチに対する PR として作成します。

📘 **手順の詳細**: [ブランチ・デプロイ運用手順書 - develop ブランチでのバグ修正](./branching-and-deployment-runbook.md#develop-ブランチでのバグ修正)

---

## GitHub 設定変更などの特殊な master PR 運用

### 概要

`.github/` 以下のファイル変更など、GitHub の挙動を変更する変更や、リポジトリー設定に関わる変更を、**バージョンを更新せずに** master と develop の両方に迅速に適用したいケースがあります。

### 適用対象

このフローは以下のような変更に適用されます：

- **GitHub リポジトリー設定ファイル**:
  - `.github/CODEOWNERS` - コードオーナー設定
  - `.github/workflows/*.yml` - GitHub Actions ワークフロー定義
  - `.github/ISSUE_TEMPLATE/*` - Issue テンプレート
  - `.github/PULL_REQUEST_TEMPLATE.md` - PR テンプレート
  - `.github/dependabot.yml` - Dependabot 設定
- **CI/CD 設定ファイル**:
  - CI/CD パイプラインの設定変更で、アプリケーションコードに影響しないもの
- **その他のリポジトリー管理ファイル**:
  - プロジェクト固有のリポジトリー管理に関わるファイルで、アプリケーションの動作に影響しないもの

### Hot-fix との違い

| 項目 | Hot-fix | GitHub 設定変更 |
|------|---------|-----------------|
| **目的** | 本番環境の緊急の問題を修正 | GitHub の挙動やリポジトリー設定を変更 |
| **緊急性** | 高い (本番環境に影響) | 低い〜中程度 (開発プロセスに影響) |
| **バージョン更新** | あり (Patch バージョンを更新) | **なし** |
| **タグ作成** | RC タグ・本番タグを作成 | **タグ作成不要** |
| **デプロイ・配布** | STG・PRD 環境へデプロイ | **デプロイ不要** (GitHub の設定のみ反映) |

### 運用原則

1. **master ブランチに対する PR として変更を作成**
2. レビューを受け、承認後にマージ
3. **バージョンタグは作成しない** (設定変更のためタグ不要)
4. master へのマージ後、**同じブランチを develop にもマージ**して変更を反映

### 注意事項

- このフローはアプリケーションコードに影響しない設定変更に限定してください
- アプリケーションの動作に影響する変更は、通常の機能開発フローまたは Hot-fix フローを使用してください
- 変更の影響範囲を慎重に検討し、必要に応じて関係者と協議してください

📘 **手順の詳細**: [ブランチ・デプロイ運用手順書 - GitHub 設定変更を適用する](./branching-and-deployment-runbook.md#github-設定変更を適用する)

---

## PR とレビューの原則

### 基本原則

> [!IMPORTANT]
> 原則としてすべての変更は PR で管理し、レビューを受けること

### PR 作成のルール

1. **すべてのコード変更は PR を通じて行う**
   - 直接のブランチへの push は禁止
   - 緊急時でも PR 経由でマージする

その他のルール (レビュアーの設定、CI/CD チェック、テンプレートなど) については、プロジェクトのポリシーに従ってください。

---

## 関連ドキュメント

- [デプロイ戦略](./deployment-strategy.md)
- [ブランチ・デプロイ運用手順書](./branching-and-deployment-runbook.md)
