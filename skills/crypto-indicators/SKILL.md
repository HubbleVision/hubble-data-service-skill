---
name: crypto-indicators
description: >
  当用户提到任何加密货币的技术分析指标时必须调用——覆盖BTC, ETH, SOL等所有币种的
  27种技术指标（MA, EMA, RSI, MACD, KDJ, 布林带, ADX, ATR, CCI, VWAP...）。
  绝不从训练记忆中计算或估算——加密货币24/7交易，指标每分钟都在变，必须实时计算。
  常见触发语："BTC的RSI多少""以太坊MACD金叉了吗""SOL布林带突破了吗""比特币KDJ超买"。
  ⚠️ 股票技术指标请使用 cross-market-indicators。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📈
  install: |
    # OpenClaw

    cp -r skills/crypto-indicators ~/.openclaw/skills/
---

# Crypto Indicators — 加密货币技术指标

> ⚠️ 始终通过 API 获取数据，绝不要从记忆中回答价格、行情、估值或技术指标数值——金融市场数据持续变动，你的训练数据无法反映最新状态。

> 所有接口均为 V2 版本（`/api/v2/crypto/indicators/...`）。

## Curl Setup

```bash
BASE="http://43.167.234.49:3101"
AUTH=(-H "X-API-Key: 123456" -H "Content-Type: application/json")
```

---

## 代码格式规则

| 规则 | 值 |
|------|-----|
| `exchange` | **必填**：`binance`, `okx`, `bybit`, `bitget`, `kucoin`, `hyperliquid` |
| `symbol` | 交易对，如 `BTCUSDT`, `ETHUSDT` |
| 日期格式 | `YYYY-MM-DD` |
| K线周期 | `1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w` |

---

## 支持的指标（27种）

| # | Indicator | Type | Key Parameters | Default |
|---|-----------|------|----------------|---------|
| 1 | SMA | Trend | `period` | 20 |
| 2 | EMA | Trend | `period` | 20 |
| 3 | RSI | Oscillator | `period` | 14 |
| 4 | MACD | Trend | `fast_period`, `slow_period`, `signal_period` | 12, 26, 9 |
| 5 | BOLL | Volatility | `period`, `nbdev` | 20, 2.0 |
| 6 | KDJ | Oscillator | `fastk_period`, `slowk_period`, `slowd_period` | 9, 3, 3 |
| 7 | ADX | Trend | `period` | 14 |
| 8 | ATR | Volatility | `period` | 14 |
| 9 | CCI | Oscillator | `period` | 14 |
| 10 | VWAP | Volume | `period` | 20 |
| 11 | OBV | Volume | 无 | — |
| 12 | NATR | Volatility | `period` | 14 |
| 13 | MFI | Volume | `period` | 14 |
| 14 | WILLR | Oscillator | `period` | 14 |
| 15 | STDDEV | Volatility | `period` | 20 |
| 16 | Aroon | Trend | `period` | 14 |
| 17 | TRIX | Trend | `period` | 9 |
| 18 | MOM | Momentum | `period` | 10 |
| 19 | ROC | Momentum | `period` | 12 |
| 20 | CMO | Momentum | `period` | 14 |
| 21 | AD | Volume | 无 | — |
| 22 | HT Trendline | Trend | 无 | — |
| 23 | PPO | Trend | `fast_period`, `slow_period` | 12, 26 |
| 24 | SAR | Trend | `acceleration`, `maximum` | 0.02, 0.2 |
| 25 | Stoch | Oscillator | `k_period`, `d_period`, `smooth_period` | 14, 3, 3 |
| 26 | ULTOSC | Oscillator | `period1`, `period2`, `period3` | 7, 14, 28 |
| 27 | ADOSC | Volume | `fast_period`, `slow_period` | 3, 10 |

---

## 端点详情

### POST /api/v2/crypto/indicators/batch — 批量指标（推荐）

一次请求计算多个指标，**禁止逐个调用单指标端点**。

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `exchange` | string | **Yes** | — | 交易所 |
| `symbol` | string | **Yes** | — | 交易对 |
| `interval` | string | No | `1d` | `1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w` |
| `limit` | int | No | `100` | 记录数上限 |
| `start` | string | No | — | 起始日期 **YYYY-MM-DD** |
| `end` | string | No | — | 结束日期 **YYYY-MM-DD** |
| `indicators` | array | **Yes** | — | 指标列表 |

### GET /api/v2/crypto/indicators/{type} — 单指标

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `exchange` | string | **Yes** | 交易所 |
| `symbol` | string | **Yes** | 交易对 |
| `interval` | string | No | 默认 `1d` |
| `period` | int | No | 周期参数 |
| `limit` | int | No | 默认 100 |

---

## Batch Params 映射

| Indicator | params | 示例 |
|-----------|--------|------|
| sma, ema, rsi, atr, cci, vwap, natr, mfi, willr, stddev, aroon, trix | `[period]` | `[14]` |
| mom, roc, cmo | `[period]` | `[10]` |
| macd | `[fast, slow, signal]` | `[12, 26, 9]` |
| boll | `[period, nbdev]` | `[20, 2.0]` |
| kdj | `[fastk, slowk, slowd]` | `[9, 3, 3]` |
| ppo, adosc | `[fast, slow]` | `[12, 26]` |
| sar | `[acceleration, maximum]` | `[0.02, 0.2]` |
| stoch | `[k, d, smooth]` | `[14, 3, 3]` |
| ultosc | `[period1, period2, period3]` | `[7, 14, 28]` |
| obv, ad, ht_trendline | 无 params | 省略 |

---

## 调用示例

### 批量接口（推荐）

```bash
# BTC：RSI + MACD + BOLL + KDJ
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/crypto/indicators/batch" -d '{
  "exchange": "binance",
  "symbol": "BTCUSDT",
  "interval": "1d",
  "limit": 100,
  "indicators": [
    {"type": "rsi", "params": [14]},
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "boll", "params": [20, 2.0]},
    {"type": "kdj", "params": [9, 3, 3]}
  ]
}'

# ETH：4h 级别 MACD + EMA
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/crypto/indicators/batch" -d '{
  "exchange": "binance",
  "symbol": "ETHUSDT",
  "interval": "4h",
  "limit": 200,
  "indicators": [
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "ema", "params": [20]}
  ]
}'
```

### 单指标接口

```bash
# BTC RSI
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicators/rsi?exchange=binance&symbol=BTCUSDT&interval=1d&period=14&limit=100"

# ETH MACD
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicators/macd?exchange=binance&symbol=ETHUSDT&interval=1d&fast_period=12&slow_period=26&signal_period=9&limit=100"

# SOL BOLL
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicators/boll?exchange=binance&symbol=SOLUSDT&interval=1d&period=20&nbdev=2.0&limit=100"

# 无参数指标：OBV
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicators/obv?exchange=binance&symbol=BTCUSDT&interval=1d&limit=100"
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 400 (缺 exchange) | `exchange` 必填 | 加上 `exchange`: `binance` |
| 400 (日期格式) | 用了 `YYYYMMDD` | 指标端点用 `YYYY-MM-DD` |
| 404 | 指标类型拼写错误 | 小写：`rsi`, `macd`, `boll` |
| 数据不足 | K线数量不够计算指标 | 增大 `limit` |
