# ブランチ運用手順書

本ドキュメントは、Git-flow に準拠したブランチ運用の手順を定めたものです。

## 目次

1. [ブランチ構成](#ブランチ構成)
2. [ブランチ命名規則](#ブランチ命名規則)
3. [機能開発フロー](#機能開発フロー)
4. [リリースブランチの運用](#リリースブランチの運用)
5. [Hot-fix の運用](#hot-fix-の運用)
6. [非 Hot-fix 修正の運用](#非-hot-fix-修正の運用)
7. [PR とレビューの原則](#pr-とレビューの原則)

---

## ブランチ構成

Git-flow に準拠し、以下のブランチを使用します。

| ブランチ種別 | ブランチ名 | 説明 |
|------------|-----------|-----|
| メインブランチ | `master` | 本番リリース用。vX.Y.Z タグはこのブランチ上にのみ存在する |
| 開発ブランチ | `develop` | 次期リリースの開発用。すべての feature コミットはここにマージされる |
| 機能ブランチ | `feature/*` | 新機能開発用 |
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
- `release/v1.2.0`
- `release/v2.0.0`
- `hotfix/fix-critical-security-issue`
- `hotfix/fix-payment-error`

---

## 機能開発フロー

### 1. feature ブランチの作成

```bash
# develop ブランチから feature ブランチを作成
git checkout develop
git pull origin develop
git checkout -b feature/your-feature-name
```

### 2. 開発とコミット

```bash
# 変更をコミット
git add .
git commit -m "feat: 機能の説明"
```

> **重要**: `feat` (feature) コミットは最終的に `develop` ブランチにマージされる必要があります。`feature` ブランチで作業し、PR を通じて `develop` にマージしてください。

### 3. リモートへのプッシュ

```bash
git push origin feature/your-feature-name
```

### 4. PR の作成とレビュー

- `develop` ブランチに対する PR を作成します
- レビューを受け、承認後にマージします

---

## リリースブランチの運用

### リリースブランチの作成タイミング

- 次のリリースに含める機能が `develop` ブランチに揃った時点
- リリーススケジュールに基づいて計画的に作成

### リリースブランチの作成手順

#### 1. ローカルでの操作

```bash
# develop ブランチを最新化
git checkout develop
git pull origin develop

# リリースブランチを作成
# 例: v1.2.0 のリリースを準備する場合
git checkout -b release/v1.2.0
```

#### 2. リモートへのプッシュ

```bash
git push origin release/v1.2.0
```

### Feature-Freeze 状態

リリースブランチが作成されると、**Feature-Freeze** 状態となります。

#### Feature-Freeze 状態とは

- `release/vX.Y.Z` ブランチでは新機能の追加は禁止
- 許可される変更:
  - バグ修正
  - ドキュメント修正
  - リリースに必要な設定変更
- 新機能は次のリリースサイクルで `develop` ブランチから開発を継続

#### Feature-Freeze 中の開発

```bash
# 新機能開発は develop ブランチで継続
git checkout develop
git checkout -b feature/next-version-feature

# バグ修正は release ブランチで実施
git checkout release/v1.2.0
git checkout -b bugfix/fix-something
# 修正後、release ブランチに対する PR を作成
```

---

## Hot-fix の運用

### Hot-fix とは

本番環境で発生した緊急の問題を修正するための変更です。

### Hot-fix の手順

#### 1. Hot-fix ブランチの作成

```bash
git checkout master
git pull origin master
git checkout -b hotfix/fix-critical-issue
```

#### 2. 修正の実施

```bash
git add .
git commit -m "fix: 緊急修正の説明"
```

#### 3. PR の作成

- **master ブランチに対する PR として修正を作成**
- レビューを受け、承認後にマージ

#### 4. develop への反映

Hot-fix は `master` へのマージ後、`develop` にも反映する必要があります。

```bash
git checkout develop
git pull origin develop
git merge master
git push origin develop
```

---

## 非 Hot-fix 修正の運用

### 非 Hot-fix 修正とは

- 機能追加を含まないバグ修正や改善
- 緊急性のない修正
- リファクタリング

### 運用方法

非 Hot-fix 修正は、`release/vX.Y.{Z-1}` ブランチで Feature-Freeze 中に対応します。

#### 例: v1.2.0 リリース後に発見されたバグを v1.2.1 で修正する場合

```bash
# 現在のリリースブランチから新しいパッチブランチを作成
git checkout release/v1.2.0
git checkout -b bugfix/fix-minor-issue

# 修正を実施
git add .
git commit -m "fix: 軽微なバグ修正"

# release/v1.2.0 ブランチに対する PR を作成
git push origin bugfix/fix-minor-issue
```

### Feature-Freeze 状態での取り扱い

- 既存の `release/vX.Y.{Z-1}` ブランチに対してバグ修正のみを適用
- 機能追加は禁止
- 修正後、新しいパッチバージョン（例: v1.2.1）としてリリース

---

## PR とレビューの原則

### 基本原則

> **原則としてすべての変更は PR で管理し、レビューを受けること**

### PR 作成のルール

1. **すべてのコード変更は PR を通じて行う**
   - 直接のブランチへの push は禁止
   - 緊急時でも PR 経由でマージする

2. **適切なレビュアーを設定する**
   - 最低 1 名以上のレビューを必須とする
   - コードオーナーが設定されている場合は自動的にレビュアーに追加

3. **PR テンプレートに従う**
   - 変更内容の説明
   - テスト方法
   - 影響範囲

4. **CI/CD チェックをパスしてからマージ**
   - 自動テストがすべてパスしていること
   - リンターチェックがパスしていること

### レビューの観点

- コードの品質と可読性
- セキュリティ上の問題がないか
- パフォーマンスへの影響
- テストの網羅性
- ドキュメントの更新が必要か

---

## 関連ドキュメント

- [デプロイ運用手順書](./deployment-guide.md)
