---
description: Codex CLI経由でgpt-image-1（Images 2.0）を使って画像生成する。APIキー不要・ChatGPT Plus認証で動く。/codex-image で起動。
---

# Codex画像生成スキル (/codex-image)

ChatGPT Plus認証済みのCodex CLIを使い、`gpt-image-1`（Images 2.0）で画像を生成する。
**OpenAI APIキー不要**。`codex exec` 1行で完結。

---

## 前提条件

- Codex CLI インストール済み: `C:\Users\yokan\AppData\Roaming\npm\codex`
- ChatGPT Plus アカウントでログイン済み（auth: `~/.codex/auth.json`）
- 生成画像の保存先: `~/.codex/generated_images/{session_id}/ig_*.png`

---

## 実行方法

```bash
codex exec --skip-git-repo-check "{画像生成プロンプト}"
```

### 注意事項
- `windows sandbox: spawn setup refresh` エラーが出ても**無視してOK**
  → シェル実行のサンドボックスが失敗するだけで、画像生成ツール自体は動く
- Codexは `imagegenスキル` を内部で呼び出し、ChatGPT Plus経由でAPIを叩く
- 生成後は `~/.codex/generated_images/` の最新フォルダに保存される

---

## 標準ワークフロー

### Step 1: 生成
```bash
codex exec --skip-git-repo-check "{プロンプト}"
```

### Step 2: 画像を取得して開く
```bash
FOLDER=$(ls ~/.codex/generated_images/ | tail -1)
FILE=$(ls ~/.codex/generated_images/$FOLDER/)
start "C:/Users/yokan/.codex/generated_images/$FOLDER/$FILE"
```

### Step 3: Claude Codeで確認（任意）
Readツールで画像パスを読み込めばチャット内（モデル側）で確認可能。
※Antigravityのフロントエンドは現状画像レンダリング非対応なので、Windowsフォトで開くのが実用的。

---

## プロンプトのコツ

| 目的 | 追加する指定 |
|---|---|
| アニメ絵 | `Anime illustration style, clean linework, bold outlines` |
| フォトリアル | `photorealistic, natural lighting, shallow depth of field` |
| 日本人モデル | `Beautiful Japanese woman, natural makeup, soft lighting` |
| マンガ風 | `manga style, black and white, screen tone, dynamic composition` |
| 吹き出し付き | `speech bubble saying '{セリフ}', no other text` |
| 縦長（ショート動画） | `9:16 vertical format, 1080x1920` |
| 横長（アイキャッチ） | `16:9 landscape, 1200x630px` |

---

## 実例

```bash
# アニメキャラ2人のコミックシーン
codex exec --skip-git-repo-check "Anime illustration. [キャラ描写と構図]. Comic manga style."

# 日本人女性ポートレート
codex exec --skip-git-repo-check "Beautiful Japanese woman, black semi-long hair, natural makeup, photorealistic, soft lighting, portrait."

# ブログ記事アイキャッチ
codex exec --skip-git-repo-check "1200x630px, 16:9, manga/anime style flat design. [記事テーマのビジュアル]. TEXT OVERLAY: '[タイトル文字]'"
```

---

## バッチ生成（複数画像）

複数プロンプトをループで回す場合:

```bash
for prompt in "画像1のプロンプト" "画像2のプロンプト"; do
  codex exec --skip-git-repo-check "$prompt"
done
```

生成画像は `~/.codex/generated_images/` 配下にセッションIDごとに保存される。

---

## トラブルシューティング

| エラー | 原因 | 対処 |
|---|---|---|
| `windows sandbox: spawn setup refresh` | Windows Home非対応のサンドボックス機能 | 無視してOK（画像生成には影響なし） |
| 画像が生成されない | ChatGPT Plus期限切れ | `~/.codex/auth.json` を確認、再ログイン |
| プロンプト通りにならない | 英語で詳細に書く | 日本語混在よりフル英語の方が精度高い |
