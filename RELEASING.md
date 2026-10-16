# 新しいバージョンを公開する手順

アプリを直したら、この2つのファイルを**必ずセットで**更新します。

| ファイル | 役割 |
| --- | --- |
| `index.html` | アプリ本体。先頭の `APP_VERSION` が「このファイルのバージョン」 |
| `version.json` | 公開中の最新バージョンと更新内容。各先生のアプリがこれを読みに来る |

`version.json` の `latest` が `index.html` の `APP_VERSION` より新しいとき、
各先生の画面で右上の「更新」ボタンがオレンジ色（🆕 更新があります）に変わります。

---

## 手順

### 1. `index.html` のバージョンを上げる

ファイルの上のほう（`const SK = 'sekigae_v2';` のすぐ上）にあります。

```js
const APP_VERSION = '1.2.0';        // ← 上げる
const APP_VERSION_DATE = '2026-10-01'; // ← 公開日
```

バージョンの付け方の目安（数字は好きに決めて構いませんが、**必ず前より大きく**します）

- 小さな修正・文言直し → `1.1.0` → `1.1.1`
- 機能の追加・改善 → `1.1.0` → `1.2.0`
- 大きな作り直し → `1.1.0` → `2.0.0`

### 2. `version.json` を書き換える

`latest` と `releasedAt` を 1 で入れた値にそろえ、`releases` の**先頭**に新しい項目を足します。

```json
{
  "latest": "1.2.0",
  "releasedAt": "2026-10-01",
  "fileName": "index.html",
  "pageUrl": "https://edu-tools-jp.github.io/sekigae/",
  "downloadUrl": "https://edu-tools-jp.github.io/sekigae/index.html",
  "releases": [
    {
      "version": "1.2.0",
      "date": "2026-10-01",
      "title": "印刷まわりの改善",
      "important": false,
      "changes": [
        "印刷デザインに「和紙」を追加しました。",
        "名簿の読み込みで、性別が空欄でもエラーにならないよう直しました。"
      ]
    }
  ]
}
```

- `changes` に書いた文章が、そのまま先生の画面の「更新内容」に並びます。**専門用語を避けて、変わったことを一言ずつ**書きます。
- `title` は省略できます。
- `important` を `true` にすると、ボタンが赤くなり「重要」バッジが付きます。**不具合の修正など、必ず入れてほしいときだけ**使ってください。
- 古い項目は消さずに残します（何世代か前のバージョンを使っている先生には、その間の更新内容がまとめて表示されます）。

### 3. コミットして `main` に push する

```
git add index.html version.json
git commit -m "v1.2.0 印刷まわりの改善"
git push origin main
```

GitHub Pages に反映されるまで1〜2分ほどかかります。

### 4. 反映を確認する

ブラウザで https://edu-tools-jp.github.io/sekigae/version.json を開き、`latest` が新しい値になっていれば完了です。

各先生のアプリは、**起動時（前回の確認から6時間以上あいているとき）** に自動で確認します。
すぐ試したいときは「設定」タブ →「🔄 いま更新を確認する」を押します。

---

## 注意

- **`APP_VERSION` と `version.json` の `latest` は必ずそろえる。** `version.json` だけ上げると、新しいファイルに差し替えた先生の画面にも「更新があります」が出続けます（アプリ側で中身のバージョンも確認しているため、その場合はダウンロードが止まり「まだ新しくなっていないようです」と表示されます）。
- `version.json` は**必ず正しいJSON**にする。書式が壊れていると、更新のお知らせが出なくなります。
  確認：`python3 -c "import json;json.load(open('version.json'))"`
- ファイル名（`index.html`）とURLは変えない。変えると、すでに配った版から更新を取りに行けなくなります。
  どうしても変える場合は `version.json` の `downloadUrl` も合わせて直します。
- 一度公開したバージョン番号は使い回さない。
