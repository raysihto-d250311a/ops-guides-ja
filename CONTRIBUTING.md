# Contributing Guide

Thank you for your interest in contributing to this repository. Please follow the guidelines below.

## Commit Messages and PR Titles

Commit messages and Pull Request titles must be written in **English** and follow [Conventional Commits](https://www.conventionalcommits.org/).

### Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Available Types

| type | Description |
|------|-------------|
| `build` | Changes that affect the build system or external dependencies |
| `chore` | Other changes that don't modify src or test files |
| `ci` | Changes to CI configuration files and scripts |
| `docs` | Documentation only changes |
| `feat` | A new feature |
| `fix` | A bug fix |
| `perf` | A code change that improves performance |
| `refactor` | A code change that neither fixes a bug nor adds a feature |
| `revert` | Reverts a previous commit |
| `style` | Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc) |
| `test` | Adding missing tests or correcting existing tests |
| `ux` | Changes related to user experience |

### Scope Examples

You can specify a scope after the type to clarify the area of change:

- `chore(deps)`: Dependency updates
- `chore(release)`: Release related changes
- `fix(api)`: API bug fixes
- `feat(auth)`: Authentication feature additions

### Examples

```
feat(auth): add login functionality

fix: resolve null pointer exception in user service

docs: update README with installation instructions

chore(deps): upgrade lodash to 4.17.21
```

## Terminology Rules

Japanese terminology should follow the Cabinet Office announcements and Microsoft style guides. Use long vowel marks (prolonged sound marks).

| Correct | Incorrect |
|---------|-----------|
| リポジトリー | リポジトリ |
| ディレクトリー | ディレクトリ |
| カテゴリー | カテゴリ |
| プロパティー | プロパティ |
| メモリー | メモリ |

## Language

- **Japanese**: Documents, code comments, and explanations should be written in Japanese.
- **English**: Commit messages, PR titles, README.md, and CONTRIBUTING.md should be written in English.
