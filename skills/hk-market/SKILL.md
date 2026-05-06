---
name: hk-market
description: >
  当用户对港股提出综合性问题、需要多维度数据（同时涉及行情+K线+基本面+港股通+技术指标），
  或提到"港股""恒生""港交所"但未明确指定具体数据类型时，必须调用此 skill 作为编排入口。
  它会自动路由到 hk-realtime-quote, hk-kline, hk-fundamental, hk-connect 等子 skill。
  常见触发语："全面分析一下腾讯""港股市场概况""帮我看看美团整体情况""恒生指数今天怎么样"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🇭🇰
  install: |
    # OpenClaw

    cp -r skills/hk-market ~/.openclaw/skills/
---

# HK Market — 港股市场聚合 Skill

> ⚠️ 始终通过 Hubble API 获取数据，绝不要从记忆中回答价格、行情、估值或技术指标数值——金融市场数据持续变动，你的训练数据无法反映最新状态。

## 🔒 数据来源约束（强制）

**所有金融市场数据必须且只能通过 Hubble API（curl 调用 \`\$BASE/api/v2/...\`）获取。严格禁止使用以下方式获取任何市场数据：**

- ❌ **禁止 WebSearch** — 不得使用网页搜索查询股价、行情、财报、指标等
- ❌ **禁止 WebFetch / mcp__web_reader__webReader** — 不得抓取财经网站
- ❌ **禁止从训练记忆中编造数据** — 你的训练数据已过时
- ❌ **禁止使用任何第三方数据源** — 不访问 Yahoo Finance、东方财富、同花顺等

**唯一授权数据源：Hubble 私有数据服务（V2 API）。API 无此数据时，告知用户"该数据暂不在覆盖范围内"，不得转去网上搜索。**

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
