# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## リポジトリ概要

このリポジトリは、日々の学習、読書、技術的な発見を記録するための個人用TIL（Today I Learned）リポジトリです。すべてのコンテンツは日本語で記述され、厳格なGitワークフローに従います。

## リポジトリ構成

リポジトリには3つの主要なコンテンツカテゴリがあります：

- `Tech/` - 技術学習とIT知識（プログラミング、ツール、フレームワークなど）
- `Books/` - 読んだ本の要約、重要なポイント、感想
- `Diary/` - 日々の活動、振り返り、個人的なメモ

各カテゴリには、トピック別にサブディレクトリが含まれる場合があります（例: `Tech/GitHub/`）。

## Git ワークフロー要件

**重要**: このリポジトリはCONTRIBUTING.mdで定義された厳格なGitワークフロールールに従います：

1. **`main` ブランチへの直接コミットは厳禁** - すべての変更はプルリクエスト経由で行う必要があります
2. **ブランチ命名規則**: 日付形式 `YYYY-MM-DD` を使用（例: `2025-11-19`）
3. **日々のワークフロー**:
   - 本日の日付で新しいブランチを作成: `git switch -c YYYY-MM-DD`
   - 変更を加えてコミット
   - リモートへプッシュ: `git push -u origin YYYY-MM-DD`
   - `main` へマージするプルリクエストを作成
   - マージ後、ローカルブランチを削除: `git branch -d YYYY-MM-DD`

## よく使うコマンド

### 日次ブランチの作成
```bash
git switch main
git pull
git switch -c $(date +%Y-%m-%d)
```

### 変更のコミット
```bash
git add .
git commit -m "feat: YYYY-MM-DD の TIL 記録を追加"
git push -u origin $(date +%Y-%m-%d)
```

### プルリクエストの作成（GitHub CLI使用）
```bash
gh pr create --base main --head YYYY-MM-DD --title "feat: YYYY-MM-DD の TIL 記録" --body ""
```

## コンテンツガイドライン

- すべてのコンテンツは日本語のMarkdownで記述されます
- 各学習エントリは、適切なカテゴリ内の個別のMarkdownファイルとして作成します
- ファイル名はコンテンツを説明的に表現します（例: `GitHub CLIを使ったPullRequestの作り方.md`）
