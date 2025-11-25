# Contributing Guide

このリポジトリーへの貢献に感謝します。以下のガイドラインに従ってください。

## コミットメッセージと PR タイトル

コミットメッセージと Pull Request のタイトルは **英語** で記述し、[Conventional Commits](https://www.conventionalcommits.org/) に準拠してください。

### フォーマット

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### 使用する type

| type | 説明 |
|------|------|
| `build` | ビルドシステムや外部依存関係に影響する変更 |
| `chore` | その他の変更（ソースやテストの変更を含まない） |
| `ci` | CI 設定ファイルやスクリプトの変更 |
| `docs` | ドキュメントのみの変更 |
| `feat` | 新機能の追加 |
| `fix` | バグ修正 |
| `perf` | パフォーマンスを向上させるコード変更 |
| `refactor` | バグ修正や機能追加を含まないコード変更 |
| `revert` | 以前のコミットを取り消す変更 |
| `style` | コードの意味に影響しない変更（空白、フォーマット、セミコロンなど） |
| `test` | テストの追加や既存テストの修正 |
| `ux` | ユーザーエクスペリエンスに関する変更 |

### scope の例

type に続けて scope を指定することで、変更の範囲を明確にできます：

- `chore(deps)`: 依存関係の更新
- `chore(release)`: リリース関連の変更
- `fix(api)`: API に関するバグ修正
- `feat(auth)`: 認証機能の追加

### 例

```
feat(auth): add login functionality

fix: resolve null pointer exception in user service

docs: update README with installation instructions

chore(deps): upgrade lodash to 4.17.21
```

## 用語規則

日本語の用語は、内閣告示およびマイクロソフトのスタイルガイドに準拠してください。長音符号を含む表記を使用します。

| 正しい表記 | 誤った表記 |
|-----------|-----------|
| リポジトリー | リポジトリ |
| ディレクトリー | ディレクトリ |
| カテゴリー | カテゴリ |
| プロパティー | プロパティ |
| メモリー | メモリ |

## 言語

- **日本語**: ドキュメント、コードコメント、説明などは日本語で記述してください。
- **英語**: コミットメッセージ、PR タイトル、README.md は英語で記述してください。
