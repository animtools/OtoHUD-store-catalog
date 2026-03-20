# OtoHUD ストアカタログ（GitHub Pages 用）

このフォルダは **OtoHUD 本体リポジトリとは別**に、GitHub 上でストア一覧を HTTPS 配信するための最小構成です。  
`catalog.json` と `store/` 以下の画像を公開し、アプリを再ビルドせずに内容を更新できます。

## リモートリポジトリ

- **GitHub:** [animtools/OtoHUD-store-catalog](https://github.com/animtools/OtoHUD-store-catalog)
- **クローン URL:** `https://github.com/animtools/OtoHUD-store-catalog.git`

初回プッシュ例（未設定の場合）:

```bash
cd store-catalog
git remote add origin https://github.com/animtools/OtoHUD-store-catalog.git
git push -u origin main
```

## GitHub Pages の有効化

1. リポジトリの **Settings → Pages** を開く。
2. **Source**: Deploy from a branch。
3. **Branch**: `main` / **Folder**: `/ (root)` を保存。

数分後、次の URL で配信されます。

- カタログ: `https://animtools.github.io/OtoHUD-store-catalog/catalog.json`
- 画像例: `https://animtools.github.io/OtoHUD-store-catalog/store/official-ambience-autumn-v1.svg`

`catalog.json` 内の `thumbnailUrl` は上記 Pages ベースの絶対 URL に合わせてあります。

## OtoHUD アプリ側の設定

`OtoHUD/.env` に次を設定（ビルド時に埋め込みます）。

```env
VITE_STORE_CATALOG_URL=https://animtools.github.io/OtoHUD-store-catalog/catalog.json
```

`VITE_STORE_CATALOG_URL` を**未設定**のビルドでは、同梱の `catalog.json` は読みません（ストアタブは「ストア準備中」の案内のみ）。リモート URL を設定したうえで取得に失敗した場合は、「カタログを取得できませんでした」の案内になります。

## 商品の追加・編集

手順・`catalog.json` のフィールド一覧は **[ADD-PRODUCTS.md](./ADD-PRODUCTS.md)** を参照してください。

## 更新の流れ

1. `catalog.json` や `store/` の画像を編集してコミット。
2. `git push`。Pages が再デプロイされたらアプリは次回起動時（ストアタブ初回表示時）に新しい JSON を取得します。

※ 既にストアを一度開いたセッションではキャッシュされた結果が残る場合があります。完全な再取得が必要ならアプリ再起動、または将来の「再読み込み」機能を検討してください。
