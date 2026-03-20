# ストアへの商品追加ガイド

このリポジトリ（**OtoHUD-store-catalog**）の `catalog.json` と `store/` を GitHub Pages で公開し、OtoHUD が `VITE_STORE_CATALOG_URL` から取得します。**アプリの再ビルドは不要**です（ユーザーは次回起動などで新しい JSON を読みます）。

JSON のスキーマは **OtoHUD アプリ本体**の型定義が正です。フィールドを増やす・`category` を増やす場合は本体側の更新が必要です。

- `OtoHUD/src/types/store.ts` … `PackCatalog` / `PackCatalogItem` / `PackCategory`

---

## 前提チェックリスト

1. このリポジトリをクローンし、`main`（または作業ブランチ）で編集できる。
2. **GitHub Pages** が有効で、`catalog.json` がブラウザで開ける。  
   例: `https://animtools.github.io/OtoHUD-store-catalog/catalog.json`
3. 利用者・開発者の OtoHUD `.env` に `VITE_STORE_CATALOG_URL` が設定されている。

---

## リポジトリ内の構成

```
（このリポジトリのルート）
├── catalog.json      … 商品一覧（必須）
├── store/            … サムネイル画像（任意・推奨）
│   └── *.webp / *.svg など
├── README.md
└── ADD-PRODUCTS.md   … 本ガイド
```

- **`catalog.json`** … ルートに1ファイル。公開 URL は `https://<ユーザー>.github.io/<リポジトリ名>/catalog.json`。
- **`store/`** … サムネ用。公開 URL は `https://<ユーザー>.github.io/<リポジトリ名>/store/<ファイル名>`。

---

## `catalog.json` のルート

| キー | 型 | 説明 |
|------|-----|------|
| `version` | number | カタログ形式の版。現状 `1` でよい。 |
| `updated_at` | string | ISO 8601（UTC 推奨）。運用上の「最終更新」メモ用。 |
| `items` | 配列 | 商品オブジェクトの配列。空配列も有効。 |

---

## 1 商品あたりのフィールド（`items[]`）

| キー | 必須 | 説明 |
|------|------|------|
| `id` | はい | 一意の ID。英数字・ハイフン推奨（例: `official-foo-v1`）。画像ファイル名と揃えると運用しやすい。 |
| `displayName` | はい | ストアに表示する名称。 |
| `description` | はい | 説明文（カード内表示。長すぎないように）。 |
| `category` | はい | 次のいずれか **のみ**: `ambience` / `voice` / `sfx` / `theme` |
| `tags` | はい | 文字列の配列。空配列 `[]` 可。 |
| `thumbnailUrl` | いいえ | **GitHub Pages 上の絶対 URL** を推奨。例: `https://animtools.github.io/OtoHUD-store-catalog/store/my-pack.webp` |
| `thumbnailEmoji` | いいえ | 画像が無い・読み込み失敗時の代替。省略時は `displayName` の先頭1文字。 |
| `price` | はい | 円（整数）。`0` は無料表示。 |
| `currency` | はい | 現状 **`"JPY"` のみ**想定。 |
| `purchaseUrl` | はい | BOOTH 等の**商品ページ URL**。「ストアを開く」から遷移。 |
| `is_new` | はい | `true` なら「NEW」バッジ・新着バナー候補。 |
| `is_premium_included` | はい | `true` なら「Premium で利用可」表示。 |
| `published_at` | はい | ISO 8601。新着バッジの未読カウントと組み合わせて使われる。 |
| `version` | はい | 商品の版表記（セマンティック風の文字列で可）。 |

### カテゴリを増やしたい場合

ここに無い `category` 文字列を入れると、アプリの型・フィルタとずれる可能性があります。**新カテゴリが必要なときは** OtoHUD 本体の `src/types/store.ts` とストア UI を更新し、アプリをリリースしてください。

---

## 商品を追加する手順（推奨フロー）

### 1. サムネイルを用意する

- 比率 **16:10**、幅 **約 440px** 程度の WebP / SVG などが目安。
- ファイル名例: `store/my-new-pack-v1.webp`

（解像度・形式の詳細は OtoHUD 本体リポジトリの `docs/store-catalog-images.md` を参照。）

### 2. `store/` にファイルを置く

このリポジトリの `store/` に保存し、Git にコミットする。

### 3. `thumbnailUrl` を決める

Pages のベース URL + `/store/` + ファイル名です。

```text
https://animtools.github.io/OtoHUD-store-catalog/store/my-new-pack-v1.webp
```

**注意:** 自分のユーザー名・リポジトリ名に合わせて置き換えてください。

### 4. `catalog.json` の `items` にオブジェクトを追加する

既存の `items` の1件をコピーし、`id`・文言・URL・価格などを書き換えるのが安全です。

### 5. JSON の妥当性を確認する

- カンマ抜け・ダブルクォートの閉じ忘れがないか。
- `category` / `currency` が許容値か。

`git push` のあと、ブラウザで `catalog.json` を開き、パースエラーがないことを確認します。

### 6. コミットして `git push`

GitHub Pages の反映後（通常数分以内）、ユーザーは**アプリ再起動後**などに新一覧を取得します。開発者は `.env` に同じ `VITE_STORE_CATALOG_URL` を入れた `npm run tauri dev` で確認できます。

### 7. `updated_at` を更新する（推奨）

変更のたびに `updated_at` を現在時刻（UTC）に更新すると、トラブル時の切り分けがしやすくなります。

---

## 「NEW」と `published_at`

- `is_new: true` の商品は UI 上で NEW 表示の対象になります。
- アプリは「最後にストアタブを見た時刻」を保存し、**その時刻より新しい `published_at` の NEW 商品**をバッジ件数に含めます（実装: OtoHUD の `storeStore.ts`）。

新商品では `published_at` を公開日時に合わせ、`is_new: true` にします。古い商品の NEW を外すときは `is_new: false` にします。

---

## よくあるつまずき

| 現象 | 確認すること |
|------|----------------|
| アプリが「カタログを取得できませんでした」 | `catalog.json` の URL が 200 で開けるか。typo・Pages 未反映。 |
| 画像が出ず絵文字や頭文字になる | `thumbnailUrl` が **絶対 URL** か。push 後にその URL をブラウザで直接開けるか。 |
| フィルタにカテゴリが出ない | 表示中の `items` に含まれる `category` だけが選択肢になる。 |

---

## 関連

- リポジトリの [README.md](./README.md) … Pages の有効化・`VITE_STORE_CATALOG_URL`
- 画像仕様の詳細 … OtoHUD 本体 `docs/store-catalog-images.md`
