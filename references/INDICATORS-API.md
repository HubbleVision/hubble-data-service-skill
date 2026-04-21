# Technical Indicators API Reference

## Supported Indicators (27 total)

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

## Common Parameters (all indicators)

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `market` | string | **Yes** | — | `cn`(A股), `hk`(港股), `us`(美股) |
| `symbol` | string | **Yes** | — | 股票代码 |
| `interval` | string | No | `1d` | `1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w` |
| `limit` | int | No | `100` | 记录数上限 |
| `start` | string | No | — | 起始日期 **YYYY-MM-DD** |
| `end` | string | No | — | 结束日期 **YYYY-MM-DD** |

---

## 批量接口（推荐）

`POST /api/v2/indicators/batch` — 一次请求计算多个指标，禁止逐个调用单独端点。

```bash
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/indicators/batch" -d '{
  "market": "cn",
  "symbol": "000001.SZ",
  "interval": "1d",
  "limit": 100,
  "indicators": [
    {"type": "rsi", "params": [14]},
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "boll", "params": [20, 2.0]},
    {"type": "kdj", "params": [9, 3, 3]},
    {"type": "sma", "params": [20]}
  ]
}'
```

### Batch params 映射

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

## 单指标接口模板

`GET /api/v2/indicators/{type}?market=&symbol=&interval=1d&period=&limit=100`

无参数指标（OBV, AD, HT Trendline）省略 `period`：
```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/obv?market=cn&symbol=000001.SZ&interval=1d&limit=100"
```

多参数指标（MACD, BOLL, KDJ, SAR 等）使用具体参数名：
```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/macd?market=cn&symbol=000001.SZ&interval=1d&fast_period=12&slow_period=26&signal_period=9&limit=100"
```

## 指标信息查询

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/info"
```

## 多市场示例

```bash
# A股 RSI
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/rsi?market=cn&symbol=000001.SZ&interval=1d&period=14&limit=100"
# 港股 MACD
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/macd?market=hk&symbol=00700.HK&interval=1d&limit=100"
# 美股 BOLL
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/boll?market=us&symbol=AAPL&interval=1d&period=20&nbdev=2.0&limit=100"
```
