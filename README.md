# OtoHUD ストアカタログ（GitHub Pages 用）

このフォルダは **OtoHUD 本体リポジトリとは別**に、GitHub 上でストア一覧を HTTPS 配信するための最小構成です。  
`catalog.json` と `store/` 以下の画像を公開し、アプリを再ビルドせずに内容を更新できます。

## 事前準備

1. GitHub で **新しい空のリポジトリ**を作成する（例: `otohud-store-catalog`）。
2. このディレクトリの **`catalog.json` 内の `USERNAME` / `REPO` を実際のユーザー名とリポジトリ名に置換**する。  
   - 例: `https://yourname.github.io/otohud-store-catalog/store/...`
3. リモートを追加してプッシュする。

```bash
cd store-catalog
git init -b main
git add .
git commit -m "Initial store catalog"
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

## GitHub Pages の有効化

1. リポジトリの **Settings → Pages** を開く。
2. **Source**: Deploy from a branch。
3. **Branch**: `main` / **Folder**: `/ (root)` を保存。

数分後、次の URL で `catalog.json` が配信されます（例）。

- `https://YOUR_USERNAME.github.io/YOUR_REPO/catalog.json`

## OtoHUD アプリ側の設定

`OtoHUD/.env` に次を設定（ビルド時に埋め込みます）。

```env
VITE_STORE_CATALOG_URL=https://YOUR_USERNAME.github.io/YOUR_REPO/catalog.json
```

未設定の場合や取得失敗時は、同梱の `/data/catalog.json` にフォールバックします。

## 更新の流れ

1. `catalog.json` や `store/` の画像を編集してコミット。
2. `git push`。Pages が再デプロイされたらアプリは次回起動時（ストアタブ初回表示時）に新しい JSON を取得します。

※ 既にストアを一度開いたセッションではキャッシュされた結果が残る場合があります。完全な再取得が必要ならアプリ再起動、または将来の「再読み込み」機能を検討してください。
