---
description: "MyBlogの進捗記録を自動生成"
---

以下の手順で、MyBlog プロジェクトの進捗記録を自動生成してください：

## 1. 前回の記録を確認

`MyApp/MyBlog/` ディレクトリ内の既存の進捗ファイル（`YYYY-MM-DD.md` 形式）を確認：
- 進捗ファイルが存在する場合：最新のファイルから `対象コミット範囲` の最新ハッシュを取得
- 進捗ファイルが存在しない場合：初回として扱う（全コミットを対象）

## 2. my-blog リポジトリの最新コミットを取得

GitHub API を使用して、my-blog リポジトリの main ブランチのコミット履歴を取得：

```bash
gh api repos/mrmrtmrn/my-blog/commits --jq '.[] | "\(.sha) \(.commit.message | split("\n")[0])"'
```

## 3. GitHub Project から issue 情報を取得

GitHub Project（#9）から issue の状態を取得：

```bash
gh project item-list 9 --owner mrmrtmrn --format json --limit 100
```

### 記録済み Done issue の確認

`MyApp/MyBlog/.done-issues.json` から記録済みの Done issue 番号を取得。

### issue の分類

- **In Progress**: 現在 `In Progress` 状態のissue → 全て表示
- **Done（新規）**: `Done` 状態かつ `.done-issues.json` に未記録 → 新規完了として表示
- **Done（記録済み）**: `.done-issues.json` に記録済み → スキップ

## 4. 新しいコミット/issueの有無を確認

- 前回のハッシュ以降に新しいコミットがあるか確認
- 新規の Done issue または In Progress の issue があるか確認
- **どちらもない場合**：「MyBlog に新しい変更はありません。」と表示して終了
- **いずれかがある場合**：次のステップへ進む

## 5. TIL リポジトリのブランチ名から日付を取得

```bash
git branch --show-current
```

ブランチ名が `YYYY-MM-DD` 形式であることを確認。

## 6. 進捗ファイルを生成

`MyApp/MyBlog/進捗テンプレート.md` をベースに、`MyApp/MyBlog/YYYY-MM-DD.md` を作成：

### 自動入力する内容：

1. **タイトル**: `# YYYY-MM-DD MyBlog 進捗`
2. **対象コミット範囲**: `前回のハッシュ（短縮形7文字）` → `最新のハッシュ（短縮形7文字）`
   - 初回の場合は `初回` → `最新のハッシュ`
   - コミットがない場合は `変更なし`
3. **今日やったこと（コミット）**: 前回のハッシュ以降のコミットメッセージを箇条書きで列挙
   - 例：
     ```
     - [abc1234] Initial commit
     - [def5678] Add Docker configuration
     ```
4. **Issue 進捗**:
   - **完了した issue**: 新規 Done の issue を箇条書き
     ```
     - #2 Dockerの設定理解
     ```
   - **進行中の issue**: In Progress の issue を箇条書き
     ```
     - #3 Railsで使っているgemの理解
     ```

### 手動で埋める内容（空欄のまま）：

- 概要
- 学んだこと・気づき
- 次にやること
- 参考リンク

## 7. 記録済み Done issue を更新

新規 Done として記録した issue 番号を `MyApp/MyBlog/.done-issues.json` に追加：

```json
{
  "description": "myblog-progress で記録済みの Done issue を管理するファイル",
  "recorded_done_issues": [1, 2]
}
```

## 8. 結果を報告

作成したファイルのパスと、対象となったコミット数・issue数を報告：

```
✅ 進捗ファイルを作成しました: MyApp/MyBlog/YYYY-MM-DD.md

【コミット】X 件
- [abc1234] Initial commit
- [def5678] Add Docker configuration

【Issue】
- 完了: Y 件
- 進行中: Z 件

「概要」「学んだこと・気づき」「次にやること」は手動で記入してください。
```

---

**重要:**
- すべて日本語で記述
- コミットハッシュは短縮形（7文字）を使用
- ファイル名の日付はTILリポジトリのブランチ名に準拠
- Done issue は `.done-issues.json` で重複を防ぐ
