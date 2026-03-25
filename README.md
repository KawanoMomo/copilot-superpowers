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

## 動作要件

- **VS Code 1.104 以降**（Agent Skills / Custom Agents / Subagents の完全サポート）
- **GitHub Copilot 拡張機能**（最新版）
- **GitHub Copilot のプラン**: Agent Mode を利用できるプラン（Pro+, Business, Enterprise のいずれか）

## VS Code の設定

### 1. settings.json に追加（推奨設定）

`Ctrl+Shift+P` → `Preferences: Open User Settings (JSON)` で以下を追加:

```jsonc
{
    // Agent Mode を有効化
    "chat.agent.enabled": true,

    // Agent Skills を有効化
    "chat.useAgentSkills": true,

    // Custom Instructions (.github/copilot-instructions.md) を有効化
    "github.copilot.chat.codeGeneration.useInstructionFiles": true,

    // エージェントの最大リクエスト数を拡張（デフォルト25、複雑なワークフロー向けに増加）
    "chat.agent.maxRequests": 50,

    // Skills / Agents / Prompts の読み込みディレクトリ
    "chat.agentSkillsLocations": {
        ".github/skills": true
    },
    "chat.agentFilesLocations": {
        ".github/agents": true
    },
    "chat.promptFilesLocations": {
        ".github/prompts": true
    },

    // モノレポの場合：親リポジトリのカスタマイズも検出
    "chat.useCustomizationsInParentRepositories": true,

    // ターミナルコマンドの自動承認（任意）
    // TDD サイクル（テスト実行→修正→再テスト）を円滑にするため、
    // 安全なコマンドのみ自動承認し、破壊的なコマンドはブロックする
    "chat.tools.terminal.autoApprove": {
        "/^git\\s+(status|diff|log|show|add|commit|push|pull|fetch|checkout|branch)\\b/": true,
        "/^npm\\s+(test|run\\s+lint|run\\s+build|install)\\b/": true,
        "/^pytest\\b/": true,
        "/^python\\s+-m\\s+pytest\\b/": true,
        "/^cargo\\s+(build|test|check|clippy)\\b/": true,
        "/^make\\b/": true,
        "rm": false,
        "del": false,
        "/^rm\\s+-rf/": false
    }
}
```

> **注意:** `chat.tools.terminal.autoApprove` は任意です。設定しない場合、ターミナルコマンドの実行ごとに承認ダイアログが表示されます。破壊的なコマンド（`rm`, `del`）は `false` でブロックすることを推奨します。

### 2. モデル選択

Copilot Chat パネル上部のモデルセレクターからモデルを選択できます。スキルとの相性推奨:

| 用途 | 推奨モデル |
|---|---|
| 計画策定・設計（Planner） | Claude Opus / GPT-5 |
| 実装（Implementer） | Claude Sonnet / GPT-5 mini |
| レビュー（Reviewer） | Claude Opus / GPT-5 |
| デバッグ（Debugger） | Claude Sonnet |

## 使い方

### スラッシュコマンド

Copilot Chat で以下のコマンドが使えます:

| コマンド | 説明 |
|---|---|
| `/brainstorm` | アイデア設計セッションを開始 |
| `/write-plan` | 仕様から実装計画を作成 |
| `/execute-plan` | 計画をタスクごとに実行 |
| `/debug` | 体系的デバッグを開始 |

### カスタムエージェントの使用

Copilot Chat のエージェントセレクター（`@` ドロップダウン）から選択:

1. **@Planner** — 計画策定。完了後 `[Start Implementation]` ボタンで Implementer へ遷移
2. **@Implementer** — TDD で実装。完了後 `[Review Spec Compliance]` で Spec Reviewer へ
3. **@Spec Reviewer** — 仕様準拠を確認。問題あれば `[Fix Issues]` で Implementer に戻す
4. **@Code Reviewer** — コード品質を確認。承認後 `[Finish Branch]` で完了
5. **@Debugger** — 根本原因を調査。修正が必要なら `[Implement Fix]` で Implementer へ

### Agent Skills の自動発火

Skills は Copilot が自動的にタスクの内容から関連スキルを判断して読み込みます。例えば:

- バグ修正の話をすると → `systematic-debugging` が自動的に適用
- 新機能の実装を始めると → `test-driven-development` が自動的に適用
- 完了を宣言しようとすると → `verification-before-completion` が自動的に適用

### 基本ワークフロー

```
新機能開発:
  /brainstorm → 設計を合意
  ↓
  /write-plan → 計画書を作成
  ↓
  @Planner → [Start Implementation] → @Implementer
  ↓
  @Implementer → [Review Spec Compliance] → @Spec Reviewer
  ↓
  @Spec Reviewer → [Proceed to Code Review] → @Code Reviewer
  ↓
  @Code Reviewer → [Finish Branch] → マージ or PR

バグ修正:
  /debug → 根本原因を特定
  ↓
  @Debugger → [Implement Fix] → @Implementer (TDD で修正)
```

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
