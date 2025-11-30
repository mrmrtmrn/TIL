---
description: "Learning Frontend の進捗記録を自動生成"
---

以下の手順で、Learning Frontend プロジェクトの進捗記録を自動生成してください：

## 1. 前回の記録を確認

`Tech/Frontend/LearningFrontend/.last-commit.json` から前回のコミットハッシュを取得：

- ファイルが存在する場合：`lastCommitHash` の値を取得
- ファイルが存在しない、または `lastCommitHash` が `null` の場合：初回として扱う（全コミットを対象）

## 2. learning-frontend リポジトリの最新コミットを取得

GitHub API を使用して、learning-frontend リポジトリの main ブランチのコミット履歴を取得：

```bash
gh api repos/mrmrtmrn/learning-frontend/commits --jq '.[] | "\(.sha) \(.commit.message | split("\n")[0])"'
```

## 3. 新しいコミットの有無を確認

- 前回のハッシュ以降に新しいコミットがあるか確認
- **新しいコミットがない場合**：「learning-frontend に新しい変更はありません。」と表示して終了
- **新しいコミットがある場合**：次のステップへ進む

## 4. コミット差分を取得して内容を分析

前回のハッシュ以降の各コミットについて、GitHub API で差分を取得：

```bash
# 特定のコミットの差分を取得
gh api repos/mrmrtmrn/learning-frontend/commits/{commit_sha}
```

### 分析する内容：

1. **logs/ ディレクトリの変更**：
   - 追加・変更されたファイルの内容を収集
   - 学習記録や気づきとして記録されている内容を抽出
   - これを「学んだこと・気づき」セクションにまとめる

2. **サンプルコードディレクトリの変更** (`javascript/`, `typescript/`, `react/`)：
   - 追加・変更されたファイルを確認
   - どんなコードを実装したかを把握
   - これを「実装したコード」セクションにまとめる

## 5. TIL リポジトリのブランチ名から日付を取得

```bash
git branch --show-current
```

ブランチ名が `YYYY-MM-DD` 形式であることを確認。

## 6. 進捗ファイルを生成または更新

### 6-A. 既存ファイルがない場合（新規作成）

`Tech/Frontend/LearningFrontend/進捗テンプレート.md` をベースに、`Tech/Frontend/LearningFrontend/YYYY-MM-DD.md` を作成：

#### 自動入力する内容：

1. **タイトル**: `# YYYY-MM-DD Learning Frontend 進捗`

2. **対象コミット範囲**: `前回のハッシュ（短縮形7文字）` → `最新のハッシュ（短縮形7文字）`
   - 初回の場合は `初回` → `最新のハッシュ`

3. **概要**: コミットログと差分から今日の学習内容を1〜2文で要約
   - 例：
     - 「JavaScript の配列メソッドについて学習し、map/filter/reduce のサンプルコードを実装」
     - 「TypeScript の型システムの基礎を学び、interface と type の違いを整理」

4. **今日やったこと（コミット）**: 前回のハッシュ以降のコミットメッセージを箇条書きで列挙
   - 例：
     ```
     - [abc1234] Add array methods examples
     - [def5678] Learn about map, filter, reduce
     ```

5. **学んだこと・気づき**: `logs/` ディレクトリの変更内容から収集・まとめ
   - logs/ 内のファイルの追加・変更内容を読み取る
   - 学習記録として書かれている内容を箇条書きでまとめる
   - 例：
     ```
     - map() は新しい配列を返すため、元の配列を変更しない
     - filter() は条件に合致する要素のみを抽出できる
     - reduce() は累積値を計算する際に便利
     ```

6. **実装したコード**: サンプルコードディレクトリの変更から
   - 追加・変更されたファイルとその概要を記載
   - 例：
     ```
     - javascript/array-methods.js - map/filter/reduce の基本的な使い方
     - javascript/array-advanced.js - チェーンメソッドの実装例
     ```

7. **参考リンク**: logs/ に記載されているリンクがあれば抽出、なければ空欄

### 6-B. 既存ファイルがある場合（追記モード）

同じ日付の進捗ファイル `Tech/Frontend/LearningFrontend/YYYY-MM-DD.md` が既に存在する場合は、以下の部分のみ更新：

1. **対象コミット範囲**: 最新のハッシュに更新
2. **概要**: 既存の概要に追加情報をマージして再生成
3. **今日やったこと（コミット）**: 新しいコミットを末尾に追記（重複は追加しない）
4. **学んだこと・気づき**: 新しい logs/ の内容を追記
5. **実装したコード**: 新しいサンプルコードの変更を追記
6. **参考リンク**: 新しいリンクがあれば追記

## 7. .last-commit.json を更新

最新のコミットハッシュを `.last-commit.json` に記録：

```json
{
  "lastCommitHash": "最新のコミットハッシュ（完全な40文字）",
  "lastUpdated": "YYYY-MM-DD"
}
```

## 8. 結果を報告

作成/更新したファイルのパスと、対象となったコミット数を報告：

### 新規作成の場合：
```
✅ 進捗ファイルを作成しました: Tech/Frontend/LearningFrontend/YYYY-MM-DD.md

【コミット】X 件
- [abc1234] Add array methods examples
- [def5678] Learn about map, filter, reduce

【学んだこと】logs/ から Y 件の学習記録を収集
【実装したコード】Z 件のサンプルコードを記録
```

### 追記モードの場合：
```
✅ 進捗ファイルを更新しました: Tech/Frontend/LearningFrontend/YYYY-MM-DD.md

【追加コミット】X 件
- [ghi7890] 追加のコミット

【学んだこと】新規で Y 件の学習記録を追加
【実装したコード】新規で Z 件のサンプルコードを追加
```

---

**重要:**
- すべて日本語で記述
- コミットハッシュは短縮形（7文字）を使用
- ファイル名の日付はTILリポジトリのブランチ名に準拠
- `.last-commit.json` で前回のコミットハッシュを管理
- 毎回必ず GitHub API を実行して最新情報を取得すること（過去の取得結果を再利用しない）
- logs/ の内容を丁寧に読み取り、学習記録として意味のある形でまとめること
