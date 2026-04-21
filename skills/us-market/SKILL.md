---
name: us-market
description: >
  美股市场全量数据服务。编排美股所有细粒度 skill：实时行情、K线、基础数据、技术指标。
  当用户提及"美股""纽交所""纳斯达克""苹果""特斯拉"且未明确指定数据维度时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🇺🇸
  install: |
    # OpenClaw

    cp -r skills/us-market ~/.openclaw/skills/
---

# US Market — 美股市场聚合 Skill

> 所有接口均为 V2 版本（`/api/v2/...`）。

## Curl Setup

```bash
BASE="http://43.167.234.49:3101"
AUTH=(-H "X-API-Key: 123456" -H "Content-Type: application/json")
```

---

## 下属 Skills

| Skill | 维度 | 触发场景 |
|-------|------|----------|
| `us-realtime-quote` | 实时行情 | "苹果现在多少钱""美股实时" |
| `us-kline` | K线 | "苹果日K""特斯拉周线" |
| `us-fundamental` | 基础数据 | "美股列表""美股交易日" |
| `cross-market-indicators` | 技术指标 | "RSI""MACD"（market=`us`） |

---

## 路由规则

```
用户输入
  │
  ├── 实时价格/现价/报价 → us-realtime-quote
  │     参数：codes（Ticker），如 AAPL
  │
  ├── K线/日线/周线/月线 → us-kline
  │     参数：symbol（Ticker），如 AAPL
  │
  ├── 美股列表/交易日 → us-fundamental
  │
  └── 技术指标/RSI/MACD... → cross-market-indicators
        参数：market=us, symbol（Ticker）
```

---

## 代码格式速查

美股代码格式统一，无歧义：

| 接口类型 | 参数名 | 格式 |
|----------|--------|------|
| securities（实时行情） | `codes` | `AAPL` |
| K线（stocks） | `symbol` | `AAPL` |
| 其他所有接口 | `tsCode` | `AAPL` |

---

## 典型 Workflow

### 个股综合分析

```bash
# 并行获取：实时行情 + K线
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=AAPL&limit=60" &
wait
```

### 多股对比

```bash
# 并行获取：多只股票实时行情 + K线
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL,TSLA,MSFT" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=AAPL&limit=30" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=TSLA&limit=30" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=MSFT&limit=30" &
wait
```

### 技术分析

```bash
# K线 + 批量指标并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=AAPL&limit=100" &
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/indicators/batch" -d '{
  "market": "us", "symbol": "AAPL", "interval": "1d", "limit": 100,
  "indicators": [
    {"type": "rsi", "params": [14]},
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "boll", "params": [20, 2.0]},
    {"type": "kdj", "params": [9, 3, 3]}
  ]
}' &
wait
```
