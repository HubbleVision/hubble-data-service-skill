---
name: crypto-market
description: >
  当用户提到任何加密货币（BTC, ETH, SOL, BNB, XRP, DOGE, ADA...）或加密货币市场相关话题时必须调用。
  覆盖K线行情、持仓量、爆仓数据、资金费率、多空比、恐惧贪婪指数、ETF资金流、巨鲸追踪等。
  绝不从训练记忆中回答——加密货币24/7无休交易，价格每秒都在变，你的训练数据已严重过时。
  常见触发语："BTC现在多少""以太坊资金费率""最近爆仓了多少""恐惧贪婪指数""比特币持仓量变化"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: ₿
  install: |
    # OpenClaw

    cp -r skills/crypto-market ~/.openclaw/skills/
---

# Crypto Market — 加密货币市场聚合 Skill

> ⚠️ 始终通过 API 获取数据，绝不要从记忆中回答价格、行情、估值或技术指标数值——金融市场数据持续变动，你的训练数据无法反映最新状态。

> 所有接口均为 V2 版本（`/api/v2/crypto/...`）。

## Curl Setup

```bash
BASE="http://43.167.234.49:3101"
AUTH=(-H "X-API-Key: 123456" -H "Content-Type: application/json")
```

---

## 代码格式规则

| 规则 | 值 |
|------|-----|
| 交易对格式 | `BTCUSDT`, `ETHUSDT`（交易所+交易对） |
| 币种 | `BTC`, `ETH`, `SOL` |
| 支持交易所 | `binance`, `okx`, `bybit`, `bitget`, `kucoin`, `hyperliquid` |
| 日期格式 | `YYYYMMDD`（部分接口） |
| K线周期 | `1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w` |

---

## 下属 Skills

| Skill | 维度 | 触发场景 |
|-------|------|----------|
| `crypto-market`（本 skill） | 全量数据 | BTC/ETH 行情、资金费率、持仓、爆仓等 |
| `crypto-indicators` | 技术指标 | "BTC RSI""以太坊 MACD" |

---

## API 端点总览

### 1. 基础行情

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/price` | 实时价格 | `symbol` |
| `GET /api/v2/crypto/marketcap` | 市值数据 | `coin` |
| `GET /api/v2/crypto/futures` | 合约行情 | `symbol` |
| `GET /api/v2/crypto/coins` | 支持币种列表 | — |
| `GET /api/v2/crypto/symbols` | 支持交易对列表 | `exchange` |
| `GET /api/v2/crypto/exchanges` | 交易所列表 | — |
| `GET /api/v2/crypto/symbols/list` | 指定交易所交易对 | `exchange` |
| `GET /api/v2/crypto/klines` | 统一K线 | `exchange`, `symbol`, `interval` |

### 2. 持仓数据 (Open Interest)

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/open-interest/list` | 持仓列表（按交易所） | `symbol` |
| `GET /api/v2/crypto/open-interest/history` | 持仓历史 | `symbol`, `exchange` |
| `GET /api/v2/crypto/open-interest/kline` | 持仓K线 | `symbol`, `exchange` |
| `GET /api/v2/crypto/open-interest/agg-history` | 币种聚合持仓历史 | `coin`；可选: `endTime`(默认当前), `size`(默认500) |
| `GET /api/v2/crypto/open-interest/agg-kline` | 聚合持仓K线 | `coin` |
| `GET /api/v2/crypto/open-interest/coin-realtime` | 实时持仓（币种） | `coin` |
| `GET /api/v2/crypto/open-interest/oi-vs-mc` | 持仓市值比 | `coin` |

### 3. 爆仓数据 (Liquidation)

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/liquidation/realtime` | 爆仓实时统计 | `coin` |
| `GET /api/v2/crypto/liquidation/history` | 爆仓统计历史 | `symbol`, `exchange` |
| `GET /api/v2/crypto/liquidation/agg-history` | 币种爆仓聚合历史 | `coin` |
| `GET /api/v2/crypto/liquidation/orders` | 爆仓订单 | `symbol` |
| `GET /api/v2/crypto/liquidation/map` | 清算地图 | `symbol` |
| `GET /api/v2/crypto/liquidation/heatmap` | 清算热力图 | `symbol` |
| `GET /api/v2/crypto/liquidation/agg-map` | 聚合清算地图 | `coin` |
| `GET /api/v2/crypto/liquidation/heatmap-symbols` | 热图支持交易对 | — |

### 4. 资金费率 (Funding Rate)

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/funding-rate/realtime` | 实时资金费率 | `symbol` |
| `GET /api/v2/crypto/funding-rate/history` | 历史结算资金费率 | `symbol`, `exchange` |
| `GET /api/v2/crypto/funding-rate/cumulative` | 累计资金费率 | `symbol`, `exchange` |
| `GET /api/v2/crypto/funding-rate/weighted` | 加权资金费率 | `coin` |
| `GET /api/v2/crypto/funding-rate/kline` | 资金费率K线 | `symbol`, `exchange` |
| `GET /api/v2/crypto/funding-rate/heatmap` | 资金费率热力图 | — |
| `GET /api/v2/crypto/funding-rate/symbol-history` | 交易对资金费率历史 | `symbol` |

### 5. 多空比 (Long/Short)

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/long-short/exchange` | 交易所多空比 | `symbol` |
| `GET /api/v2/crypto/long-short/account` | 多空持仓人数比 | `symbol` |
| `GET /api/v2/crypto/long-short/top-trader` | 大户多空比(持仓量) | `symbol` |
| `GET /api/v2/crypto/long-short/top-trader-account` | 大户多空比(账户数) | `symbol` |
| `GET /api/v2/crypto/long-short/taker-ratio` | 主动买卖多空比 | `coin` |
| `GET /api/v2/crypto/long-short/kline` | 多空比K线 | `symbol`, `exchange` |

### 6. CVD / 主动买卖

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/cvd/kline` | CVD K线 | `symbol`, `exchange` |
| `GET /api/v2/crypto/buy-sell/count` | 主动买卖笔数 | `symbol`, `exchange` |
| `GET /api/v2/crypto/buy-sell/volume` | 主动买卖量 | `symbol`, `exchange` |
| `GET /api/v2/crypto/buy-sell/amount` | 主动买卖额 | `symbol`, `exchange` |
| `GET /api/v2/crypto/buy-sell/agg-count` | 聚合买卖笔数 | `coin` |
| `GET /api/v2/crypto/buy-sell/agg-volume` | 聚合买卖量 | `coin` |
| `GET /api/v2/crypto/buy-sell/agg-amount` | 聚合买卖额 | `coin` |

### 7. ETF

| Endpoint | 说明 |
|----------|------|
| `GET /api/v2/crypto/etf/btc` | 美股 BTC ETF |
| `GET /api/v2/crypto/etf/eth` | 美股 ETH ETF |
| `GET /api/v2/crypto/etf/btc-flow` | BTC ETF 净流入流出 |
| `GET /api/v2/crypto/etf/eth-flow` | ETH ETF 净流入流出 |

### 8. 市场指标

| Endpoint | 说明 |
|----------|------|
| `GET /api/v2/crypto/indicator/fear-greed` | 恐惧贪婪指数 |
| `GET /api/v2/crypto/indicator/ahr999` | AHR999 指标 |
| `GET /api/v2/crypto/indicator/two-year-ma` | 两年MA乘数 |
| `GET /api/v2/crypto/indicator/btc-dominance` | BTC 市值占比 |
| `GET /api/v2/crypto/indicator/puell` | 普尔系数 |
| `GET /api/v2/crypto/indicator/pi-cycle` | Pi 循环 |
| `GET /api/v2/crypto/indicator/altcoin-index` | 山寨指数 |
| `GET /api/v2/crypto/indicator/grayscale` | 灰度持仓 |
| `GET /api/v2/crypto/rsi/screener` | RSI 选币器 |

### 9. 新闻资讯

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/news/list` | 新闻列表 | 可选: `page`(默认1), `pageSize`(默认10), `type`(默认1), `lang`(默认zh), `isPopular`(默认false); 必填: `search` |
| `GET /api/v2/crypto/news/detail` | 新闻详情 | `id` |
| `GET /api/v2/crypto/news/search` | 新闻全文搜索 | `keyword` |
| `GET /api/v2/crypto/news/latest` | 最新新闻 | `limit` |

### 10. 巨鲸追踪 & 大额订单

| Endpoint | 说明 |
|----------|------|
| `GET /api/v2/crypto/whale/positions` | 鲸鱼持仓列表 (HyperLiquid) |
| `GET /api/v2/crypto/whale/activity` | 鲸鱼最新动态 |
| `GET /api/v2/crypto/large-order/market` | 大额市价订单 |
| `GET /api/v2/crypto/large-order/limit` | 大额限价订单 |

### 11. 排行榜

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/ranking/open-interest` | 持仓排行 | `period`, `exchange` |
| `GET /api/v2/crypto/ranking/liquidation` | 爆仓排行 | `period`, `exchange` |
| `GET /api/v2/crypto/ranking/price-change` | 价格变化排行 | `period`, `exchange` |
| `GET /api/v2/crypto/ranking/volume` | 交易量排行 | `period`, `exchange` |

### 12. 订单簿 & 订单流

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/order-book/by-symbol` | 交易对差值列表 | `symbol` |
| `GET /api/v2/crypto/order-book/by-exchange` | 交易所聚合差值 | `exchange` |
| `GET /api/v2/crypto/order-book/heatmap` | 挂单流动性热力图 | `symbol` |
| `GET /api/v2/crypto/order-flow/lists` | 订单流列表 | `symbol` |

### 13. 资金流 & 净头寸

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `GET /api/v2/crypto/capital-flow/realtime` | 实时资金流入流出 | 可选: `productType`; `coin`, `page`, `size` 等 |
| `GET /api/v2/crypto/capital-flow/history` | 历史资金流入流出 | `coin` |
| `GET /api/v2/crypto/net-positions` | 净多头和净空头 | `symbol` |

---

## 调用示例

### BTC 综合行情

```bash
# 并行获取：实时价格 + 资金费率 + 持仓 + 恐惧贪婪
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/price?symbol=BTCUSDT&exchange=binance" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/funding-rate/realtime?symbol=BTCUSDT&exchange=binance" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/open-interest/coin-realtime?coin=BTC" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicator/fear-greed" &
wait
```

### BTC 技术分析

```bash
# K线 + 技术指标并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/klines?exchange=binance&symbol=BTCUSDT&interval=1d&limit=100" &
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/crypto/indicators/batch" -d '{
  "exchange": "binance", "symbol": "BTCUSDT", "interval": "1d", "limit": 100,
  "indicators": [
    {"type": "rsi", "params": [14]},
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "boll", "params": [20, 2.0]},
    {"type": "kdj", "params": [9, 3, 3]}
  ]
}' &
wait
```

### 市场概览

```bash
# 并行获取：恐惧贪婪 + AHR999 + BTC dominance + ETF
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicator/fear-greed" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicator/ahr999" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicator/btc-dominance" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/etf/btc-flow" &
wait
```

### 爆仓 & 资金费率分析

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/liquidation/realtime?coin=BTC" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/liquidation/agg-history?coin=BTC" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/funding-rate/weighted?coin=BTC" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/funding-rate/heatmap" &
wait
```

### 巨鲸 & 大单

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/whale/positions" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/whale/activity" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/large-order/market?symbol=BTCUSDT&exchange=binance" &
wait
```

---

## 路由规则

```
用户输入
  │
  ├── BTC/ETH/SOL 价格/行情 → price + marketcap
  ├── 资金费率 → funding-rate/*
  ├── 持仓量/OI → open-interest/*
  ├── 爆仓/清算 → liquidation/*
  ├── 多空比/做多做空 → long-short/*
  ├── 买卖比/CVD/主动买卖 → buy-sell/* + cvd/*
  ├── 恐惧贪婪/AHR999/指标 → indicator/*
  ├── ETF/灰度/贝莱德 → etf/*
  ├── 新闻/快讯 → news/*
  ├── 巨鱼/大户 → whale/*
  ├── 大单/主力 → large-order/*
  ├── 排行/排名 → ranking/*
  ├── 订单簿/挂单 → order-book/*
  ├── 资金流入流出 → capital-flow/*
  ├── RSI选币 → rsi/screener
  └── 技术指标 → crypto-indicators（exchange + symbol）
```

