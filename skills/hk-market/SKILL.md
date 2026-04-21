---
name: hk-market
description: >
  港股市场全量数据服务。编排港股所有细粒度 skill：实时行情、K线、基础数据、港股通、技术指标。
  当用户提及"港股""HKEX""恒生""腾讯"且未明确指定数据维度时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🇭🇰
  install: |
    # OpenClaw

    cp -r skills/hk-market ~/.openclaw/skills/
---

# HK Market — 港股市场聚合 Skill

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
| `hk-realtime-quote` | 实时行情 | "腾讯现在多少钱""港股实时" |
| `hk-kline` | K线 | "腾讯日K""港交所周线" |
| `hk-fundamental` | 基础数据 | "港股列表""港股交易日" |
| `hk-connect` | 港股通 | "港股通Top10""北向资金""南向持仓" |
| `cross-market-indicators` | 技术指标 | "RSI""MACD"（market=`hk`） |

---

## 路由规则

```
用户输入
  │
  ├── 实时价格/现价/报价 → hk-realtime-quote
  │     参数：codes（纯数字），如 00700
  │
  ├── K线/日线/周线/月线 → hk-kline
  │     参数：symbol（带 .HK），如 00700.HK
  │     ⚠️ 必须用 startDate+endDate，不用 limit
  │
  ├── 港股列表/交易日 → hk-fundamental
  │
  ├── 港股通/北向资金/南向持仓 → hk-connect
  │     参数：tsCode（A股代码格式），如 600519.SH
  │
  └── 技术指标/RSI/MACD... → cross-market-indicators
        参数：market=hk, symbol（带 .HK）
```

---

## 代码格式速查

| 接口类型 | 参数名 | 格式 |
|----------|--------|------|
| securities（实时行情） | `codes` | 纯数字 `00700` |
| K线（stocks） | `symbol` | `00700.HK` |
| 其他所有接口 | `tsCode` | `00700.HK` |
| 港股通接口 | `tsCode` | A股格式 `600519.SH` |

---

## 典型 Workflow

### 个股综合分析

```bash
# 并行获取：实时行情 + K线
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/stocks?symbol=00700.HK&startDate=20260301&endDate=20260405" &
wait
```

### 港股通资金分析

```bash
# 并行获取：Top10 + 每日成交 + 持仓
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-top10?tsCode=600519.SH" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-daily?startDate=20260301&endDate=20260405" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/hold?tsCode=600519.SH" &
wait
```

### 技术分析

```bash
# K线 + 批量指标并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/stocks?symbol=00700.HK&startDate=20260101&endDate=20260405" &
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/indicators/batch" -d '{
  "market": "hk", "symbol": "00700.HK", "interval": "1d", "limit": 100,
  "indicators": [
    {"type": "rsi", "params": [14]},
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "boll", "params": [20, 2.0]}
  ]
}' &
wait
```

---

## 端点黑名单 — 禁止调用

| ❌ 不存在的端点 | ✅ 替代方案 |
|-----------------|------------|
| `/api/v2/hkstock/daily` | `/api/v2/hkstock/stocks`（参数名 `symbol`） |
