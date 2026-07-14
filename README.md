# 精密栄養学 用語辞書（precision-nutrition-glossary）

精密栄養学・オーソモレキュラー栄養療法の視点で、**血液検査の各データ（検査項目）を手軽に調べられる用語辞書** Web アプリです。
各項目の解説だけでなく、**項目どうしの関連（例: 鉄代謝パネル内のフェリチン↔TSAT↔TIBC↔血清鉄）をクリックでたどれる**ことを重視しています。

- 依存ゼロ・ビルドゼロの静的サイト（バニラ JavaScript）
- 用語データは `data/*.json` に分離して管理
- 検索（用語名・略称・かな・タグ）／カテゴリ絞り込み／関連項目ナビ／用語ごとのURL（`#/term/<id>`）

姉妹リポジトリ [`mhd-apps/heatstroke-risk-check`](https://github.com/mhd-apps/heatstroke-risk-check) と同じ「単一 `index.html` + CSS 変数テーマ」の流儀を踏襲しています。

## 使い方

### 公開（GitHub Pages）
リポジトリの **Settings → Pages** で、Source を `main` ブランチ（ルート）に設定すると `https://mhd-apps.github.io/precision-nutrition-glossary/` で公開できます。

### ローカルで見る
`fetch()` で JSON を読み込むため、`index.html` をファイルとして直接ダブルクリックで開くと（ブラウザの CORS 制限で）データを読めません。簡易サーバ経由で開いてください。

```bash
# リポジトリのルートで
python3 -m http.server 8000
# → ブラウザで http://localhost:8000 を開く
```

## ディレクトリ構成

```
index.html            アプリ本体（UI・検索・ルーティング）
data/
  terms.json          用語データ（1項目 = 1オブジェクト）
  categories.json     カテゴリ定義（表示順・色・説明）
README.md
LICENSE               MIT
```

## データの追加・編集

用語を増やすときは `data/terms.json` にオブジェクトを追加するだけです（コード変更は不要）。

```jsonc
{
  "id": "ferritin",              // 一意なID（related の参照キーになる。半角英数とハイフン）
  "term": "フェリチン",           // 表示名
  "reading": "ふぇりちん",         // 読み（かな検索用）
  "abbr": "Ferritin",            // 略称・英語名（英語検索用）
  "unit": "ng/mL",              // 単位
  "category": "iron",            // categories.json の id を参照
  "reference": "…",              // 一般的な基準範囲
  "optimal": "…",                // 精密栄養学での目安
  "summary": "…",                // 一覧カード用の短い説明
  "description": "…",            // 詳細解説
  "high": "…",                   // 高値のときに考えられること
  "low": "…",                    // 低値のときに考えられること
  "nutrients": ["鉄", "ビタミンC"], // 関連する栄養素
  "related": ["tsat", "tibc"],   // 関連する項目の id（相互に張ると双方向にたどれる）
  "tags": ["貧血", "鉄欠乏"],
  "sources": [{ "title": "…", "url": "https://…" }]
}
```

カテゴリを増やすときは `data/categories.json` に `{ id, name, color, description }` を追加します。

### 整合性チェック（任意）
`related` の参照先や `category` が実在するかは、次のワンライナーで確認できます。

```bash
python3 - <<'PY'
import json
terms=json.load(open('data/terms.json')); cats=json.load(open('data/categories.json'))
ids={t['id'] for t in terms}; catids={c['id'] for c in cats}
bad=[(t['id'],r) for t in terms for r in t.get('related',[]) if r not in ids]
bad+=[(t['id'],'CAT:'+t['category']) for t in terms if t['category'] not in catids]
print('NG参照:', bad or 'なし')
PY
```

## 収録データについて / 参考

初期データは以下の一般公開情報を参考に自作したものです（数値・解説はコピーではありません）。

- [awesome-biomarkers（markwk, オープンソース）](https://github.com/markwk/awesome-biomarkers) — バイオマーカー／血液検査値のオープンDB
- [MarkerDB 2.0](https://academic.oup.com/nar/article/53/D1/D1415/7899528) — 分子バイオマーカーの学術DB
- [日本人間ドック・予防医療学会 血液検査](https://www.ningen-dock.jp/inspection_blood/)
- [栄養状態を調べる血液検査 | プラスウェルネス](https://www.pluswellness.com/dictionary/checkup/004006.html)
- [血液検査からわかること | あんどう口腔クリニック](https://orthomolecular-health.jp/kensa/)
- [栄養判定に用いる血液検査 | 国立長寿医療研究センター](https://www.ncgg.go.jp/hospital/iryokankei/nst/25.html)

## 免責事項

本辞書は精密栄養学の学習・情報整理を目的とした**一般的な解説**です。基準範囲は施設や測定法によって異なり、数値の解釈や診断・治療は個々の状況により変わります。**医療上の判断は必ず医師・専門家にご相談ください。** 本辞書の情報の利用によって生じたいかなる結果についても責任を負いません。

## ライセンス

[MIT License](./LICENSE)
