# VoltaとNPMとPNPMの関係

## 学んだきっかけ

npmコマンドが使えるのは知っていたが、以前voltaもインストールしており、これらの依存関係が頭の中で整理できていなかった。さらにpnpmも導入したいと考えていたため、現在の環境を整理する必要があった。

## 調査結果：すべてvoltaが管理していた

### 現在の環境構成

```
Volta (v2.0.2)
├── Node.js v24.11.1 (デフォルト)
│   └── npm v11.6.2 (Node.js組み込み)
├── Node.js v22.13.1
├── Node.js v22.16.0
├── Node.js v20.19.5
├── yarn v4.9.2 (デフォルト)
└── pnpm v10.12.1 ← すでにインストール済み
```

### 実行バイナリの場所

```bash
$ which node
/Users/$USER/.volta/tools/image/node/24.11.1/bin/node

$ which npm
/Users/$USER/.volta/tools/image/node/24.11.1/bin/npm

$ which pnpm
/Users/$USER/.volta/bin/pnpm
```

すべてvoltaが管理するディレクトリ配下にあることが判明。

## pnpmのメリット

### 1. ディスク容量の大幅削減

- npmやyarnは各プロジェクトの`node_modules`に同じパッケージを重複して保存
- pnpmは**コンテンツアドレス可能ストア**を使用し、1つのパッケージを1回だけディスク保存
- シンボリックリンクで各プロジェクトからストアを参照
- **例**: 10個のプロジェクトでReactを使用していても、ディスクに保存されるReactは1つだけ

### 2. インストール速度が速い

- パッケージの再利用により、初回インストール後は劇的に高速化
- 並列処理が最適化されている

### 3. 厳格な依存関係管理

- npm/yarnはフラットな`node_modules`構造のため、幽霊依存（phantom dependencies）の問題がある
  - `package.json`に書いていない依存パッケージも`require()`できてしまう
- pnpmは**真の依存関係ツリー**を構築
- `package.json`に明示的に記載されていないパッケージは`require()`できない
- これにより依存関係のバグを早期発見できる

### 4. モノレポに強い

- `pnpm workspace`機能が標準装備
- 複数パッケージの管理が容易

### 5. 互換性が高い

- npmと同じコマンド体系（`pnpm install`, `pnpm add`など）
- 既存のnpmプロジェクトを簡単に移行可能

## グローバルインストールの違いと注意点

### voltaを使っている環境でのnpm install -g

voltaがインストールされている環境では、`npm install -g`で実行したグローバルインストールも**voltaの管理下に入る**。

```bash
# このコマンドを実行しても...
npm install -g @anthropic-ai/claude-code

# 実際にはvoltaが管理するディレクトリにインストールされる
# /Users/$USER/.volta/tools/image/node/24.11.1/lib/
```

### 3つの方法の比較

| 方法 | インストール先 | バージョン管理 | 推奨度 |
|------|---------------|---------------|--------|
| `volta install` | `/Users/$USER/.volta/bin/` | ✅ voltaが管理 | 高 |
| `npm install -g` | `/Users/$USER/.volta/tools/image/node/24.11.1/lib/` | ✅ voltaが管理 | 中 |
| `pnpm install -g` | `/Users/$USER/Library/pnpm/` | ❌ pnpmが独自管理 | 低 |

### pnpm install -gの問題点

`pnpm setup`を実行すると、`.zshrc`に以下が追加される：

```bash
export PNPM_HOME="/Users/$USER/Library/pnpm"
case ":$PATH:" in
  *":$PNPM_HOME:"*) ;;
  *) export PATH="$PNPM_HOME:$PATH" ;;
esac
```

これにより：
- pnpmのグローバルパッケージはvoltaの管理外になる
- PATHの優先順位によって、voltaとpnpmのバイナリが競合する可能性がある
- ツールのバージョン管理が分散してカオスになる

## 推奨される使い分け

```bash
# ✅ グローバルツール（claude, typescript, など）
volta install @anthropic-ai/claude-code
volta install typescript

# ✅ プロジェクトの依存関係（速度・容量節約のため）
cd your-project
pnpm install
pnpm add react
```

## まとめ

- voltaはNode.jsとグローバルツールのバージョン管理ツール
- npmやpnpmはパッケージマネージャー
- **グローバルツールはvolta、プロジェクト依存はpnpm**という使い分けがベスト
- voltaを使っている環境で`pnpm install -g`を使うと管理が複雑になるため避けるべき
- pnpmは主にプロジェクト単位の依存関係管理で真価を発揮する

## 参考コマンド

```bash
# voltaで管理されているツール一覧
volta list all

# 現在使用中のNode.jsバージョン確認
node --version

# バイナリの場所を確認
which node
which npm
which pnpm

# pnpmの使い方（プロジェクト内で）
pnpm install        # 依存関係をインストール
pnpm add lodash     # パッケージを追加
pnpm remove lodash  # パッケージを削除
pnpm run dev        # スクリプトを実行
```
