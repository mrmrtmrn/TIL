---
description: "本日の全ての学習記録を一括生成"
---

以下の手順で、本日の全ての学習記録を一括で自動生成してください：

## 概要

このコマンドは、以下の3つのコマンドを順番に実行します：

1. `/myblog-progress` - MyBlog の進捗記録を生成
2. `/learning-frontend-progress` - Learning Frontend の進捗記録を生成
3. `/daily-summary` - TIL リポジトリ自体の学習記録を生成

---

## 実行手順

### ステップ1: `/myblog-progress` を実行

`.claude/commands/myblog-progress.md` の指示に従って、MyBlog の進捗記録を生成：

- `MyApp/MyBlog/.done-issues.json` から前回の記録を確認
- my-blog リポジトリのコミット履歴と issue 状態を取得
- 新しい変更があれば `MyApp/MyBlog/YYYY-MM-DD.md` を生成/更新
- 変更がなければスキップ

**結果を記録**: 生成したファイルパスとコミット数・issue数を記録しておく

---

### ステップ2: `/learning-frontend-progress` を実行

`.claude/commands/learning-frontend-progress.md` の指示に従って、Learning Frontend の進捗記録を生成：

- `Tech/Frontend/LearningFrontend/.last-commit.json` から前回のコミットハッシュを取得
- learning-frontend リポジトリのコミット履歴を取得
- logs/ ディレクトリとサンプルコードの変更を分析
- 新しい変更があれば `Tech/Frontend/LearningFrontend/YYYY-MM-DD.md` を生成/更新
- `.last-commit.json` を更新
- 変更がなければスキップ

**結果を記録**: 生成したファイルパスとコミット数を記録しておく

---

### ステップ3: `/daily-summary` を実行

`.claude/commands/daily-summary.md` の指示に従って、TIL リポジトリの学習記録を生成：

- 現在のブランチ名から日付を抽出
- mainブランチとの差分を確認
- セッション中の対話内容を分析
- `Diary/YYYY/MM/YYYY-MM-DD.md` を生成/更新

**結果を記録**: 生成したファイルパスと学習トピック数を記録しておく

---

## 最終報告

全ての処理が完了したら、以下の形式で結果を報告してください：

```
✅ 本日の学習記録を一括生成しました

【MyBlog】
- ファイル: MyApp/MyBlog/YYYY-MM-DD.md
- コミット: X 件
- Issue（完了）: Y 件
- Issue（進行中）: Z 件

【Learning Frontend】
- ファイル: Tech/Frontend/LearningFrontend/YYYY-MM-DD.md
- コミット: X 件
- logs/ から学習記録を収集
- サンプルコード: Y 件

【TIL（日記）】
- ファイル: Diary/YYYY/MM/YYYY-MM-DD.md
- 学習トピック: X 件
- セッション中の学び: Y 件
```

**変更がなかった項目はスキップして報告**

---

## 注意事項

- 各コマンドは独立して実行されるため、一部が失敗しても他は続行する
- 変更がないリポジトリはスキップされる
- すべて日本語で記述
- ファイル名の日付はTILリポジトリのブランチ名に準拠
- エラーが発生した場合は、どのステップで失敗したかを明確に報告する
