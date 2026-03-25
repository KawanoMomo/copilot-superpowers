# copilot-superpowers

[superpowers](https://github.com/obra/superpowers) (by Jesse Vincent) を GitHub Copilot Agent Mode 向けに移植したスキル集です。

## 概要

ソフトウェア開発の品質を高める体系的なワークフロースキルを、VS Code の GitHub Copilot Chat (Agent Mode) で利用可能にします。

## インストール

このリポジトリの `.github/` ディレクトリを、あなたのプロジェクトのルートにコピーしてください。

```bash
# リポジトリをクローン
git clone https://github.com/KawanoMomo/copilot-superpowers.git

# .github/ ディレクトリをプロジェクトにコピー
cp -r copilot-superpowers/.github /path/to/your/project/
```

または、このリポジトリをフォークして直接利用することもできます。

## 含まれるスキル

### プロセス系スキル（Phase 1）

| スキル | 説明 |
|---|---|
| brainstorming | 実装前のアイデア設計・要件整理 |
| test-driven-development | RED-GREEN-REFACTOR サイクルの徹底 |
| systematic-debugging | 根本原因調査を先行するデバッグ手法 |
| verification-before-completion | 完了宣言前の検証エビデンス必須化 |
| receiving-code-review | コードレビュー受領時の技術的評価 |

### ツール参照系スキル（Phase 2）

| スキル | 説明 |
|---|---|
| writing-plans | 仕様から実装計画書を作成 |
| executing-plans | 計画書に基づく段階的実装 |
| finishing-a-development-branch | 開発ブランチの完了・統合 |
| using-git-worktrees | Git worktree による作業分離 |

### エージェント連携系スキル（Phase 3）

| スキル | 説明 |
|---|---|
| subagent-driven-development | サブエージェントによるタスク実行・レビュー |
| dispatching-parallel-agents | 独立タスクの並列エージェント実行 |
| requesting-code-review | コードレビューサブエージェント派遣 |
| writing-skills | 新規スキルの作成手法 |
| using-skills | スキル発見・適用のブートストラップ |

### カスタムエージェント

| エージェント | 説明 |
|---|---|
| planner | 実装計画の策定 |
| implementer | 計画に基づくコード実装 |
| spec-reviewer | 仕様準拠のレビュー |
| code-reviewer | コード品質のレビュー |
| debugger | 体系的デバッグ |

## Copilot ツール対応表

| 元 (Claude Code) | Copilot Agent Mode |
|---|---|
| `Agent` | `#agent/runSubagent` |
| `Edit` | `#edit/editFiles` |
| `Read` | `#read/readFile` |
| `Write` | `#edit/createFile` |
| `Grep` | `#search/textSearch` |
| `Glob` | `#search/fileSearch` |
| `Bash` | `#execute/runInTerminal` |
| `TodoWrite` | `#todos` |

## ライセンス

MIT License

- Copyright (c) 2026 KawanoMomo
- Copyright (c) 2025 Jesse Vincent (Original [superpowers](https://github.com/obra/superpowers))

## 謝辞

本プロジェクトは [Jesse Vincent](https://github.com/obra) 氏の [superpowers](https://github.com/obra/superpowers) を基に作成されました。
