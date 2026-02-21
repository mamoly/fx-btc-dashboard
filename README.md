# 為替 & BTC ダッシュボード

スマホで見やすい、為替レートとBTC価格のチャートダッシュボード。

GitHub Pages: https://mamoly.github.io/fx-btc-dashboard/

## 表示中の通貨ペア

| チャート | 説明 |
|---------|------|
| BTC / USD | ビットコインのドル建て価格 |
| USD / JPY | ドル円 |
| USD / THB | ドルバーツ |
| THB / JPY | バーツ円 |

## 使用技術

- **HTML/CSS/JavaScript** のみ（単一ファイル `index.html`）
- **Chart.js v4** (CDN) - チャート描画ライブラリ
- **chartjs-adapter-date-fns** (CDN) - Chart.js の日付軸アダプター
- **GitHub Pages** - ホスティング

### データ取得元（すべて無料・APIキー不要）

| データ | API |
|-------|-----|
| BTC価格 | [CoinGecko API](https://www.coingecko.com/en/api) `/api/v3/coins/bitcoin/market_chart` |
| 為替レート | [Frankfurter API](https://www.frankfurter.app/) (ECB公表レートベース) |

- BTC は CoinGecko から直接時系列データを取得
- 為替は Frankfurter API で USD → JPY, THB のレートを取得し、THB/JPY は `JPY / THB` で算出

## 通貨ペアを追加する方法

### 1. Frankfurter API で対応している通貨を追加する場合

例: USD/EUR を追加したい場合

**Step 1:** `PAIRS` 配列にエントリを追加

```js
const PAIRS = [
  { id: 'btcusd', title: 'BTC / USD', prefix: '$', decimals: 0 },
  { id: 'usdjpy', title: 'USD / JPY', prefix: '¥', decimals: 2 },
  { id: 'usdthb', title: 'USD / THB', prefix: '฿', decimals: 2 },
  { id: 'thbjpy', title: 'THB / JPY', prefix: '¥', decimals: 3 },
  { id: 'usdeur', title: 'USD / EUR', prefix: '€', decimals: 4 },  // 追加
];
```

- `id`: 内部識別子（一意であればOK）
- `prefix`: Y軸に表示する通貨記号
- `decimals`: 小数点以下の桁数

**Step 2:** Frankfurter API のクエリに通貨コードを追加

```js
const url = `...?from=USD&to=JPY,THB,EUR`;  // EUR を追加
```

**Step 3:** `fetchForex()` 内でデータを配列に格納

```js
const eur = rates.EUR;
if (eur) usdeur.push({ date, value: eur });
```

**Step 4:** `return` と `allData` オブジェクトに追加

Frankfurter API の対応通貨一覧: https://api.frankfurter.app/currencies

### 2. CoinGecko で別の暗号通貨を追加する場合

CoinGecko の `market_chart` API を使えば BTC 以外も取得可能。

```
https://api.coingecko.com/api/v3/coins/ethereum/market_chart?vs_currency=usd&days=30
```

`fetchBTC()` を参考に同様の関数を作り、`PAIRS` と `allData` に追加する。

## 注意事項

- CoinGecko 無料枠にはレート制限あり（約 10-30 req/min）
- Frankfurter API は ECB の営業日ベースのため、土日祝のデータは含まれない
- GitHub Pages の反映にはプッシュ後 1-2 分かかる場合がある
