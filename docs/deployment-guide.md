# デプロイ運用手順書

本ドキュメントは、リリースタグの運用とデプロイに関する手順を定めたものです。

## 目次

1. [タグの種類と命名規則](#タグの種類と命名規則)
2. [リリース候補（RC）の運用](#リリース候補rcの運用)
3. [ステージング環境へのデプロイ](#ステージング環境へのデプロイ)
4. [本番リリースタグの運用](#本番リリースタグの運用)
5. [アプリの審査対応](#アプリの審査対応)
6. [デプロイフロー図](#デプロイフロー図)

---

## タグの種類と命名規則

### タグ命名規則

| タグ種別 | 形式 | 説明 |
|---------|------|-----|
| リリース候補タグ | `vX.Y.Z-rc.N` | ステージング環境での動作確認用 |
| 本番リリースタグ | `vX.Y.Z` | 本番環境リリース用。**master ブランチ上にのみ存在** |

### 例

- リリース候補: `v1.2.0-rc.1`, `v1.2.0-rc.2`, `v1.2.0-rc.3`
- 本番リリース: `v1.2.0`, `v1.2.1`, `v2.0.0`

---

## リリース候補（RC）の運用

### RC タグとは

- **RC (Release Candidate)**: リリース候補
- ステージング環境での動作確認を目的としたタグ
- 問題が発見された場合は修正後に新しい RC を作成

### RC タグの作成手順

#### 1. release ブランチでの作業完了確認

```bash
git checkout release/v1.2.0
git pull origin release/v1.2.0
```

#### 2. RC タグの作成

```bash
# 最初のリリース候補
git tag v1.2.0-rc.1

# リモートへプッシュ
git push origin v1.2.0-rc.1
```

#### 3. 問題発見時の対応

問題が発見された場合は、修正後に新しい RC を作成します。

```bash
# 修正をコミット
git add .
git commit -m "fix: 発見された問題を修正"
git push origin release/v1.2.0

# 新しい RC タグを作成
git tag v1.2.0-rc.2
git push origin v1.2.0-rc.2
```

### RC 番号の採番ルール

- RC 番号は `1` から開始
- 問題発見・修正のたびにインクリメント
- 例: `v1.2.0-rc.1` → `v1.2.0-rc.2` → `v1.2.0-rc.3`

---

## ステージング環境へのデプロイ

### デプロイ対象

> **vX.Y.Z-rc.N タグはステージング環境にデプロイして動作確認すること**

### Web アプリケーションの場合

```bash
# ステージング環境へのデプロイ
# CI/CD パイプラインが rc タグを検知して自動デプロイ
# または手動でデプロイコマンドを実行

# 例: ステージング環境へのデプロイスクリプト
./deploy.sh stg v1.2.0-rc.1
```

### モバイルアプリの場合

RC タグで配布（distribute）を行い、動作確認します。

```bash
# iOS の場合: TestFlight への配布
bundle exec fastlane beta

# Android の場合: 内部テストトラックへの配布
bundle exec fastlane deploy_internal
```

### 動作確認項目

- [ ] 新機能が正しく動作すること
- [ ] 既存機能にデグレードがないこと
- [ ] パフォーマンスに問題がないこと
- [ ] エラーログに異常がないこと
- [ ] 各種連携サービスとの疎通確認

---

## 本番リリースタグの運用

### 重要な原則

> **vX.Y.Z タグは master ブランチ上にのみ存在すること**

### 本番リリースの手順

#### 1. release ブランチの最終確認

すべての RC での動作確認が完了していることを確認します。

#### 2. master ブランチへのマージ

```bash
# master ブランチをチェックアウト
git checkout master
git pull origin master

# release ブランチをマージ
git merge release/v1.2.0

# リモートへプッシュ
git push origin master
```

#### 3. 本番リリースタグの作成

```bash
# master ブランチ上でタグを作成
git tag v1.2.0

# リモートへプッシュ
git push origin v1.2.0
```

#### 4. develop ブランチへの反映

```bash
git checkout develop
git pull origin develop
git merge master
git push origin develop
```

#### 5. release ブランチの削除（オプション）

```bash
# ローカルブランチの削除
git branch -d release/v1.2.0

# リモートブランチの削除
git push origin --delete release/v1.2.0
```

---

## アプリの審査対応

### 審査フロー

モバイルアプリの場合、ストア審査があるため、以下のフローに従います。

#### 1. 最新の RC で審査提出

> **最新の RC によって審査提出すること**

```bash
# 最新の RC タグでビルド
git checkout v1.2.0-rc.3

# 審査用ビルドを作成・提出
bundle exec fastlane release_to_review
```

#### 2. 審査結果への対応

##### 審査パスの場合

> **審査パスした段階で vX.Y.Z リリースタグを打つこと**

```bash
# release ブランチを master にマージ
git checkout master
git merge release/v1.2.0
git push origin master

# 本番リリースタグを作成
git tag v1.2.0
git push origin v1.2.0
```

##### 審査リジェクトの場合

1. リジェクト理由を確認
2. release ブランチで修正を実施
3. 新しい RC タグを作成
4. 再度審査提出

```bash
# release ブランチで修正
git checkout release/v1.2.0

# 修正を実施
git add .
git commit -m "fix: 審査リジェクト対応 - 〇〇を修正"
git push origin release/v1.2.0

# 新しい RC タグを作成
git tag v1.2.0-rc.4
git push origin v1.2.0-rc.4

# 新しい RC で審査再提出
```

### 審査対応のタイムライン例

```
v1.2.0-rc.1 → ステージング確認 → 問題発見
     ↓
v1.2.0-rc.2 → ステージング確認 → OK
     ↓
v1.2.0-rc.2 → 審査提出 → リジェクト
     ↓
v1.2.0-rc.3 → 審査再提出 → パス
     ↓
master マージ → v1.2.0 タグ作成 → 本番リリース
```

---

## デプロイフロー図

### Web アプリケーションのフロー

```
[develop] 
    ↓ feature 開発完了
[release/vX.Y.Z] 作成
    ↓
[vX.Y.Z-rc.1] タグ作成 → stg デプロイ → 動作確認
    ↓ (問題あれば修正して rc.2, rc.3...)
[vX.Y.Z-rc.N] 最終確認 OK
    ↓
[master] へマージ
    ↓
[vX.Y.Z] タグ作成 → 本番デプロイ
```

### モバイルアプリのフロー

```
[develop] 
    ↓ feature 開発完了
[release/vX.Y.Z] 作成
    ↓
[vX.Y.Z-rc.1] タグ作成 → TestFlight/内部テスト配布 → 動作確認
    ↓ (問題あれば修正して rc.2, rc.3...)
[vX.Y.Z-rc.N] 最終確認 OK
    ↓
[vX.Y.Z-rc.N] で審査提出
    ↓ (リジェクトなら修正して新 RC で再提出)
審査パス
    ↓
[master] へマージ
    ↓
[vX.Y.Z] タグ作成 → ストアリリース
```

---

## チェックリスト

### リリース前チェックリスト

- [ ] すべての feature が develop にマージされている
- [ ] release ブランチが作成されている
- [ ] RC タグが作成され、ステージング環境にデプロイされている
- [ ] 動作確認が完了している
- [ ] (アプリの場合) 審査に合格している
- [ ] master ブランチへのマージ PR がレビュー済み

### リリース後チェックリスト

- [ ] vX.Y.Z タグが master ブランチ上に作成されている
- [ ] 本番環境/ストアにリリースされている
- [ ] develop ブランチに master の変更が反映されている
- [ ] リリースノートが更新されている

---

## 関連ドキュメント

- [ブランチ運用手順書](./branching-strategy.md)
