# 加密货币 API Reference

> 代码格式规则、并行调用模板见 `SKILL.md`。本文档仅补充各端点的详细参数。

## 统一K线

### Get Crypto K-lines
`GET /api/v2/crypto/klines`

统一K线接口，支持多数据源自动切换。

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `exchange` | string | **Yes** | 交易所：`binance`, `okx`, `bybit`, `bitget`, `kucoin`, `hyperliquid` |
| `symbol` | string | **Yes** | 交易对：`BTCUSDT`, `ETHUSDT` |
| `interval` | string | No | K线周期：`1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w`（默认 `1d`） |
| `limit` | int | No | 返回数量（默认 100） |
| `startTime` | string | No | 开始时间戳（毫秒） |
| `endTime` | string | No | 结束时间戳（毫秒） |
| `source` | string | No | 指定数据源（内部使用） |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/klines?exchange=binance&symbol=BTCUSDT&interval=1d&limit=100"
```

---

## 基础行情

### Get Crypto Price
`GET /api/v2/crypto/price`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | Yes | 交易对 |
| `exchange` | string | No | 交易所 |

### Get Crypto Market Cap
`GET /api/v2/crypto/marketcap`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `coin` | string | Yes | 币种 |
| `exchange` | string | No | 交易所 |

### Get Crypto Futures
`GET /api/v2/crypto/futures`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | Yes | 交易对 |
| `exchange` | string | No | 交易所 |

### Get Coins / Symbols
`GET /api/v2/crypto/coins` — 支持的币种列表（无参数）
`GET /api/v2/crypto/symbols` — 交易对列表（`exchange` 参数可选）
`GET /api/v2/crypto/exchanges` — 交易所列表（无参数）
`GET /api/v2/crypto/symbols/list` — 指定交易所交易对（`exchange` 必填）

---

## 持仓数据 (Open Interest)

> `symbol` 参数为交易对（如 `BTCUSDT`），`coin` 参数为币种（如 `BTC`）。

| Endpoint | 必填参数 | 可选参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/crypto/open-interest/list` | `symbol` | `exchange`, `period` | 持仓列表（按交易所） |
| `/api/v2/crypto/open-interest/history` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 持仓历史 |
| `/api/v2/crypto/open-interest/kline` | `symbol` | `exchange`, `interval`, `limit` | 持仓K线 |
| `/api/v2/crypto/open-interest/agg-history` | `coin` | `startDate`, `endDate`, `limit` | 币种聚合持仓历史 |
| `/api/v2/crypto/open-interest/agg-kline` | `coin` | `interval`, `limit` | 聚合持仓K线 |
| `/api/v2/crypto/open-interest/coin-realtime` | `coin` | — | 实时持仓（币种） |
| `/api/v2/crypto/open-interest/oi-vs-mc` | `coin` | `startDate`, `endDate`, `limit` | 持仓市值比 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/open-interest/history?symbol=BTCUSDT&exchange=binance&limit=30"
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/open-interest/agg-kline?coin=BTC&interval=1d&limit=30"
```

---

## 爆仓数据 (Liquidation)

| Endpoint | 必填参数 | 可选参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/crypto/liquidation/realtime` | `coin` | — | 爆仓实时统计 |
| `/api/v2/crypto/liquidation/history` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 爆仓统计历史 |
| `/api/v2/crypto/liquidation/agg-history` | `coin` | `startDate`, `endDate`, `limit` | 币种爆仓聚合历史 |
| `/api/v2/crypto/liquidation/orders` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 爆仓订单 |
| `/api/v2/crypto/liquidation/map` | `symbol` | — | 清算地图 |
| `/api/v2/crypto/liquidation/heatmap` | `symbol` | — | 清算热力图 |
| `/api/v2/crypto/liquidation/agg-map` | `coin` | — | 聚合清算地图 |
| `/api/v2/crypto/liquidation/heatmap-symbols` | — | — | 热图支持交易对 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/liquidation/realtime?coin=BTC"
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/liquidation/agg-history?coin=BTC&limit=30"
```

---

## 资金费率 (Funding Rate)

| Endpoint | 必填参数 | 可选参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/crypto/funding-rate/realtime` | `symbol` | `exchange` | 实时资金费率 |
| `/api/v2/crypto/funding-rate/history` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 历史结算资金费率 |
| `/api/v2/crypto/funding-rate/cumulative` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 累计资金费率 |
| `/api/v2/crypto/funding-rate/weighted` | `coin` | — | 加权资金费率 |
| `/api/v2/crypto/funding-rate/kline` | `symbol` | `exchange`, `interval`, `limit` | 资金费率K线 |
| `/api/v2/crypto/funding-rate/heatmap` | — | — | 资金费率热力图 |
| `/api/v2/crypto/funding-rate/symbol-history` | `symbol` | `startDate`, `endDate`, `limit` | 交易对资金费率历史 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/funding-rate/realtime?symbol=BTCUSDT&exchange=binance"
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/funding-rate/weighted?coin=BTC"
```

---

## 多空比 (Long/Short)

| Endpoint | 必填参数 | 可选参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/crypto/long-short/exchange` | `symbol` | `exchange` | 交易所多空比 |
| `/api/v2/crypto/long-short/account` | `symbol` | `exchange` | 多空持仓人数比 |
| `/api/v2/crypto/long-short/top-trader` | `symbol` | `exchange` | 大户多空比(持仓量) |
| `/api/v2/crypto/long-short/top-trader-account` | `symbol` | `exchange` | 大户多空比(账户数) |
| `/api/v2/crypto/long-short/taker-ratio` | `coin` | — | 主动买卖多空比 |
| `/api/v2/crypto/long-short/kline` | `symbol` | `exchange`, `interval`, `limit` | 多空比K线 |

---

## CVD / 主动买卖

| Endpoint | 必填参数 | 可选参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/crypto/cvd/kline` | `symbol` | `exchange`, `interval`, `limit` | CVD K线 |
| `/api/v2/crypto/buy-sell/count` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 主动买卖笔数 |
| `/api/v2/crypto/buy-sell/volume` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 主动买卖量 |
| `/api/v2/crypto/buy-sell/amount` | `symbol` | `exchange`, `startDate`, `endDate`, `limit` | 主动买卖额 |
| `/api/v2/crypto/buy-sell/agg-count` | `coin` | `startDate`, `endDate`, `limit` | 聚合买卖笔数 |
| `/api/v2/crypto/buy-sell/agg-volume` | `coin` | `startDate`, `endDate`, `limit` | 聚合买卖量 |
| `/api/v2/crypto/buy-sell/agg-amount` | `coin` | `startDate`, `endDate`, `limit` | 聚合买卖额 |

---

## ETF

| Endpoint | 说明 | 参数 |
|----------|------|------|
| `/api/v2/crypto/etf/btc` | 美股 BTC ETF | 无 |
| `/api/v2/crypto/etf/eth` | 美股 ETH ETF | 无 |
| `/api/v2/crypto/etf/btc-flow` | BTC ETF 净流入流出 | 无 |
| `/api/v2/crypto/etf/eth-flow` | ETH ETF 净流入流出 | 无 |

---

## 市场指标

| Endpoint | 说明 | 参数 |
|----------|------|------|
| `/api/v2/crypto/indicator/fear-greed` | 恐惧贪婪指数 | 无 |
| `/api/v2/crypto/indicator/ahr999` | AHR999 指标 | 无 |
| `/api/v2/crypto/indicator/two-year-ma` | 两年MA乘数 | 无 |
| `/api/v2/crypto/indicator/btc-dominance` | BTC 市值占比 | 无 |
| `/api/v2/crypto/indicator/puell` | 普尔系数 | 无 |
| `/api/v2/crypto/indicator/pi-cycle` | Pi 循环 | 无 |
| `/api/v2/crypto/indicator/altcoin-index` | 山寨指数 | 无 |
| `/api/v2/crypto/indicator/grayscale` | 灰度持仓 | 无 |
| `/api/v2/crypto/rsi/screener` | RSI 选币器 | `level`(时间级别) |

---

## 新闻资讯

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `/api/v2/crypto/news/list` | 新闻列表 | `page`, `pageSize` |
| `/api/v2/crypto/news/detail` | 新闻详情 | `id` |
| `/api/v2/crypto/news/search` | 新闻全文搜索 | `keyword`, `page`, `pageSize` |
| `/api/v2/crypto/news/latest` | 最新新闻 | `limit` |

---

## 巨鲸追踪 & 大额订单

| Endpoint | 说明 | 参数 |
|----------|------|------|
| `/api/v2/crypto/whale/positions` | 鲸鱼持仓列表 | 无 |
| `/api/v2/crypto/whale/activity` | 鲸鱼最新动态 | 无 |
| `/api/v2/crypto/large-order/market` | 大额市价订单 | `symbol`, `exchange` |
| `/api/v2/crypto/large-order/limit` | 大额限价订单 | `symbol`, `exchange` |

---

## 排行榜

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `/api/v2/crypto/ranking/open-interest` | 持仓排行 | `period`, `exchange` |
| `/api/v2/crypto/ranking/liquidation` | 爆仓排行 | `period`, `exchange` |
| `/api/v2/crypto/ranking/price-change` | 价格变化排行 | `period`, `exchange` |
| `/api/v2/crypto/ranking/volume` | 交易量排行 | `period`, `exchange` |

---

## 订单簿 & 订单流

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `/api/v2/crypto/order-book/by-symbol` | 交易对差值列表 | `symbol` |
| `/api/v2/crypto/order-book/by-exchange` | 交易所聚合差值 | `exchange` |
| `/api/v2/crypto/order-book/heatmap` | 挂单流动性热力图 | `symbol` |
| `/api/v2/crypto/order-flow/lists` | 订单流列表 | `symbol` |

---

## 资金流 & 净头寸

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `/api/v2/crypto/capital-flow/realtime` | 实时资金流入流出 | `coin` |
| `/api/v2/crypto/capital-flow/history` | 历史资金流入流出 | `coin`, `startDate`, `endDate`, `limit` |
| `/api/v2/crypto/net-positions` | 净多头和净空头 | `symbol` |
