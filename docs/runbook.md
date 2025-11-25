# 運用手順書（Runbook）

本ドキュメントは、代表的な運用シナリオにおける step-by-step 手順を記載したものです。

## 目次

1. [新機能を開発する](#新機能を開発する)
2. [リリースブランチを作成する](#リリースブランチを作成する)
3. [RC タグを作成して STG にデプロイする](#rc-タグを作成して-stg-にデプロイする)
4. [本番リリースを実施する](#本番リリースを実施する)
5. [Hot-fix を適用する](#hot-fix-を適用する)
6. [審査リジェクト対応](#審査リジェクト対応)
7. [リリース後のバグ修正（非 Hot-fix）](#リリース後のバグ修正非-hot-fix)
8. [develop ブランチでのバグ修正](#develop-ブランチでのバグ修正)

---

## 新機能を開発する

> **関連ドキュメント**: [ブランチ戦略 - 機能開発フロー](./branching-strategy.md#機能開発フロー)

### 前提条件

- `develop` ブランチが最新であること
- 作業する機能が明確であること

### 手順

#### Step 1: feature ブランチを作成

```bash
git checkout develop
git pull origin develop
git checkout -b feature/your-feature-name
```

#### Step 2: 機能を実装

```bash
# 変更を実施
# ...

git add .
git commit -m "feat: add user feature"
```

> **注意**: `feat:` プレフィックスを持つコミットは `feature` ブランチで行い、`develop` にマージしてください。

#### Step 3: リモートへプッシュ

```bash
git push origin feature/your-feature-name
```

#### Step 4: PR を作成

1. GitHub で `develop` ブランチに対する PR を作成
2. レビュアーを設定
3. レビュー承認後、マージ

---

## リリースブランチを作成する

> **関連ドキュメント**: [ブランチ戦略 - リリースブランチの運用](./branching-strategy.md#リリースブランチの運用)

### 前提条件

- 次のリリースに含める機能が `develop` ブランチに揃っていること
- リリースするバージョン番号が決定していること

### 手順

#### Step 1: develop ブランチを最新化

```bash
git checkout develop
git pull origin develop
```

#### Step 2: リリースブランチを作成

```bash
# 例: v1.2.0 のリリースを準備する場合
git checkout -b release/v1.2.0
```

#### Step 3: リモートへプッシュ

```bash
git push origin release/v1.2.0
```

#### Step 4: Feature-Freeze を宣言

- チームに Feature-Freeze 状態であることを通知
- 以降、`release/v1.2.0` ブランチには新機能の追加は禁止

---

## RC タグを作成して STG にデプロイする

> **関連ドキュメント**: [デプロイ戦略 - RC タグの作成場所](./deployment-strategy.md#rc-タグの作成場所)

### 前提条件

- リリースブランチまたは hotfix ブランチが存在すること
- リリース対象のコードがプッシュ済みであること

### 手順

#### Step 1: master ブランチをチェックアウト

```bash
git checkout master
git pull origin master
```

#### Step 2: release または hotfix ブランチをマージ

```bash
# リリースブランチの場合
git merge release/v1.2.0

# または hotfix ブランチの場合
git merge hotfix/fix-critical-issue

git push origin master
```

#### Step 3: RC タグを作成

```bash
# 最初の RC の場合
git tag v1.2.0-rc.1

# 2回目以降の RC の場合
git tag v1.2.0-rc.2
```

#### Step 4: RC タグをプッシュ

```bash
git push origin v1.2.0-rc.1
```

#### Step 5: STG 環境へデプロイ

**Web アプリの場合:**

```bash
# CI/CD パイプラインが自動デプロイ
# または手動でデプロイスクリプトを実行
./deploy.sh stg v1.2.0-rc.1
```

**モバイルアプリの場合:**

```bash
# iOS: TestFlight への配布
bundle exec fastlane beta

# Android: 内部テストトラックへの配布
bundle exec fastlane deploy_internal
```

#### Step 6: 動作確認

- [ ] 新機能が正しく動作すること
- [ ] 既存機能にデグレードがないこと
- [ ] パフォーマンスに問題がないこと
- [ ] エラーログに異常がないこと

---

## 本番リリースを実施する

> **関連ドキュメント**: [デプロイ戦略 - ブランチ・タグと環境の対応](./deployment-strategy.md#ブランチタグと環境の対応)

### 前提条件

- リリースブランチの準備が完了していること
- RC での動作確認が完了していること
- モバイルアプリの場合、RC タグのビルドが審査にパスしていること

### 手順

#### Step 1: master ブランチをチェックアウト

```bash
git checkout master
git pull origin master
```

#### Step 2: release ブランチをマージ

```bash
git merge release/v1.2.0
git push origin master
```

#### Step 3: RC タグを作成して STG で確認

```bash
git tag v1.2.0-rc.1
git push origin v1.2.0-rc.1
```

**モバイルアプリの場合:**

```bash
# iOS: TestFlight への配布・審査提出
bundle exec fastlane beta

# Android: 内部テストトラックへの配布・審査提出
bundle exec fastlane deploy_internal
```

#### Step 4: STG 環境で動作確認（モバイルアプリは審査パスを待つ）

- [ ] 新機能が正しく動作すること
- [ ] 既存機能にデグレードがないこと
- [ ] モバイルアプリの場合、審査にパスしていること

#### Step 5: 本番リリースタグを作成

> **重要**: 審査にパスした RC タグと同じコミットに対して正式タグを作成します。正式タグのビルドは審査に再提出しません。

```bash
git tag v1.2.0
git push origin v1.2.0
```

#### Step 6: PRD 環境へデプロイ

**Web アプリの場合:**

```bash
# 手動デプロイまたは承認後の半自動デプロイ
./deploy.sh prd v1.2.0
```

**モバイルアプリの場合:**

> **注意**: モバイルアプリでは、審査にパスした RC タグのビルドがそのままストアリリースされます。正式タグ `vX.Y.Z` から新たにビルドを作成して審査に再提出することはありません。

```bash
# ストアへのリリース（審査パス済みのビルドをリリース）
bundle exec fastlane release
```

#### Step 7: develop ブランチへの反映

```bash
git checkout develop
git pull origin develop
git merge master
git push origin develop
```

#### Step 8: リリースブランチの削除（オプション）

```bash
git branch -d release/v1.2.0
git push origin --delete release/v1.2.0
```

---

## Hot-fix を適用する

> **関連ドキュメント**: [ブランチ戦略 - Hot-fix の運用](./branching-strategy.md#hot-fix-の運用)

### 前提条件

- 本番環境で緊急の問題が発生していること
- 修正内容が明確であること

### 手順

#### Step 1: hotfix ブランチを作成

```bash
git checkout master
git pull origin master
git checkout -b hotfix/fix-critical-issue
```

#### Step 2: 修正を実施

```bash
git add .
git commit -m "fix: fix critical issue"
```

#### Step 3: RC タグを作成して STG で確認

```bash
git push origin hotfix/fix-critical-issue
git tag v1.2.1-rc.1
git push origin v1.2.1-rc.1
```

#### Step 4: STG 環境で動作確認

- [ ] 修正が正しく適用されていること
- [ ] 他の機能に影響がないこと

#### Step 5: master への PR を作成してマージ

1. GitHub で `master` ブランチに対する PR を作成
2. レビュー承認後、マージ

#### Step 6: 本番リリースタグを作成

```bash
git checkout master
git pull origin master
git tag v1.2.1
git push origin v1.2.1
```

#### Step 7: PRD 環境へデプロイ

```bash
./deploy.sh prd v1.2.1
```

#### Step 8: develop へ反映

```bash
git checkout develop
git pull origin develop
git merge master
git push origin develop
```

---

## 審査リジェクト対応

> **関連ドキュメント**: [デプロイ戦略 - 審査対応](./deployment-strategy.md#審査対応)

### 前提条件

- 審査リジェクトの通知を受けていること
- リジェクト理由が確認済みであること

### 手順

#### Step 1: リジェクト理由を確認

ストアからの通知を確認し、修正が必要な箇所を特定します。

#### Step 2: master ブランチ上で Hot-fix 相当の修正を実施

```bash
git checkout master
git pull origin master
git checkout -b hotfix/fix-review-rejection
```

修正を実施します：

```bash
git add .
git commit -m "fix: address app store review rejection"
git push origin hotfix/fix-review-rejection
```

#### Step 3: master への PR を作成してマージ

1. GitHub で `master` ブランチに対する PR を作成
2. レビュー承認後、マージ

#### Step 4: 新しい RC タグを作成

> **重要**: 審査に提出するのは常に `master` ブランチ上の RC タグ (`vX.Y.Z-rc.N`) からビルドしたアプリです。

```bash
git checkout master
git pull origin master
git tag v1.2.0-rc.4
git push origin v1.2.0-rc.4
```

#### Step 5: STG 環境で動作確認

```bash
bundle exec fastlane beta  # iOS の場合
```

#### Step 6: 審査再提出

> **注意**: 再提出するのは新しい RC タグ (`v1.2.0-rc.4`) のビルドです。

```bash
bundle exec fastlane release_to_review
```

#### Step 7: 審査パス後、本番リリースへ

審査にパスした RC タグと同じコミットに対して正式タグ `vX.Y.Z` を作成します。

```bash
git tag v1.2.0
git push origin v1.2.0
```

#### Step 8: develop へ反映

```bash
git checkout develop
git pull origin develop
git merge master
git push origin develop
```

---

## リリース後のバグ修正（非 Hot-fix）

> **関連ドキュメント**: [ブランチ戦略 - 非 Hot-fix 修正の運用](./branching-strategy.md#非-hot-fix-修正の運用)

### 前提条件

- 緊急性のないバグが発見されていること
- 次のパッチリリースとして対応すること

### 手順

#### Step 1: 現在のリリースブランチから bugfix ブランチを作成

```bash
git checkout release/v1.2.0
git pull origin release/v1.2.0
git checkout -b bugfix/fix-minor-issue
```

#### Step 2: 修正を実施

```bash
git add .
git commit -m "fix: fix minor bug"
```

#### Step 3: リモートへプッシュ

```bash
git push origin bugfix/fix-minor-issue
```

#### Step 4: release ブランチに対する PR を作成

1. GitHub で `release/v1.2.0` ブランチに対する PR を作成
2. レビュー承認後、マージ

#### Step 5: 新しい RC タグを作成

```bash
git checkout release/v1.2.0
git pull origin release/v1.2.0
git tag v1.2.1-rc.1
git push origin v1.2.1-rc.1
```

#### Step 6: STG で動作確認後、本番リリースへ

「本番リリースを実施する」の手順に従ってリリースを完了します。

---

## develop ブランチでのバグ修正

> **関連ドキュメント**: [ブランチ戦略 - 非 Hot-fix 修正の運用](./branching-strategy.md#非-hot-fix-修正の運用)

現在の release ブランチに関係しない、緊急性のないバグ修正や改善を行う場合の手順です。Feature-Freeze 期間中であっても、この手順で対応します。

### 前提条件

- 修正が現在の release ブランチに関係しないこと
- 緊急性のないバグや改善であること

### 手順

#### Step 1: fix または chore ブランチを作成

```bash
git checkout develop
git pull origin develop
git checkout -b fix/your-bug-fix-name
# または
git checkout -b chore/your-chore-name
```

#### Step 2: 修正を実施

```bash
# 変更を実施
# ...

git add .
git commit -m "fix: resolve minor issue"
# または
git commit -m "chore: update documentation"
```

#### Step 3: リモートへプッシュ

```bash
git push origin fix/your-bug-fix-name
```

#### Step 4: PR を作成

1. GitHub で `develop` ブランチに対する PR を作成
2. レビュアーを設定
3. レビュー承認後、マージ

> **注意**: この修正は次回のリリースに含まれます。

---

## 関連ドキュメント

- [ブランチ戦略](./branching-strategy.md)
- [デプロイ戦略](./deployment-strategy.md)
