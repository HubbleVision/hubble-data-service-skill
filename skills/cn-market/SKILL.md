---
name: cn-market
description: >
  当用户对A股提出综合性问题、需要多维度数据（同时涉及行情+K线+基本面+市场机制+指数+技术指标），
  或提到"A股""沪深""大盘"但未明确指定具体数据类型时，必须调用此 skill 作为编排入口。
  它会自动路由到 cn-realtime-quote, cn-kline, cn-fundamental, cn-market-mechanics, cn-index 等子 skill。
  常见触发语："全面分析一下茅台""A股市场概况""帮我看看宁德时代整体情况""沪深两市今天怎么样"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🇨🇳
  install: |
    # OpenClaw

    cp -r skills/cn-market ~/.openclaw/skills/
---

# CN Market — A股市场聚合 Skill

> ⚠️ 始终通过 API 获取数据，绝不要从记忆中回答价格、行情、估值或技术指标数值——金融市场数据持续变动，你的训练数据无法反映最新状态。

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
| `cn-realtime-quote` | 实时行情 | "茅台现在多少钱""实时价格" |
| `cn-kline` | K线 | "茅台日K""万科周线" |
| `cn-fundamental` | 基础数据 | "公司信息""PE""股票列表" |
| `cn-market-mechanics` | 市场机制 | "涨跌停""停复牌""新股""交易日历" |
| `cn-index` | 指数 | "上证指数""申万行业""成分股" |
| `cross-market-indicators` | 技术指标 | "RSI""MACD""布林带"（market=`cn`） |

---

## 路由规则

```
用户输入
  │
  ├── 实时价格/现价/报价 → cn-realtime-quote
  │     参数：codes（纯数字），如 600519
  │
  ├── K线/日线/周线/月线 → cn-kline
  │     参数：symbol（带后缀），如 600519.SH
  │
  ├── 公司信息/PE/PB/换手率/股票列表 → cn-fundamental
  │     参数：tsCode（带后缀），如 600519.SH
  │
  ├── 涨跌停/停复牌/IPO/交易日历/复权 → cn-market-mechanics
  │     参数：tsCode（带后缀），如 600519.SH
  │
  ├── 指数/上证/深证/申万/成分股 → cn-index
  │     参数：tsCode（带后缀），如 000001.SH
  │
  └── 技术指标/RSI/MACD/KDJ... → cross-market-indicators
        参数：market=cn, symbol（带后缀）
```

---

## 代码格式速查

| 接口类型 | 参数名 | 格式 |
|----------|--------|------|
| securities（实时行情） | `codes` | 纯数字 `600519` |
| K线（stocks） | `symbol` | `600519.SH` |
| 其他所有接口 | `tsCode` | `600519.SH` |

---

## 典型 Workflow

### 个股综合分析

```bash
# 并行获取：实时行情 + K线 + 每日指标 + 公司信息
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=600519.SH&limit=60" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/daily-basic?tsCode=600519.SH" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/company?tsCode=600519.SH" &
wait
```

### 大盘概览

```bash
# 并行获取：上证 + 深证 + 创业板 指数
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/index/daily?tsCode=000001.SH&limit=5" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/index/daily?tsCode=399001.SZ&limit=5" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/index/daily?tsCode=399006.SZ&limit=5" &
wait
```

### 技术分析

```bash
# K线 + 批量指标并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=600519.SH&limit=100" &
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/indicators/batch" -d '{
  "market": "cn", "symbol": "600519.SH", "interval": "1d", "limit": 100,
  "indicators": [
    {"type": "rsi", "params": [14]},
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "boll", "params": [20, 2.0]},
    {"type": "kdj", "params": [9, 3, 3]}
  ]
}' &
wait
```

---

## 端点黑名单 — 禁止调用

| ❌ 不存在的端点 | ✅ 替代方案 |
|-----------------|------------|
| `/api/v2/cnstock/klines` | `/api/v2/cnstock/stocks`（参数名 `symbol`） |
| `/api/v2/cnstock/industry/daily` | 不支持行业日行情 |
| `/api/v2/cnstock/sector` | 不支持板块查询 |
| `/api/v2/cnstock/industry` | 不支持行业分类 |
| `/api/v2/cnstock/summary` | `daily-basic` 获取市场概览 |
| `/api/v2/cnstock/financial` | 不支持财报数据 |
| `/api/v2/cnstock/profile` | `/api/v2/cnstock/company` |
| `/api/v2/cnstock/info` | `/api/v2/cnstock/company` |
