---
name: cn-market-mechanics
description: >
  当用户提到任何A股市场机制相关问题时必须调用：涨跌停价格计算、停复牌信息查询、
  新股IPO上市日历、A股交易日历（哪天开市/休市）、复权因子。
  绝不从训练记忆中回答——停复牌状态、IPO日历每天都在变。
  常见触发语："XX股票涨停价是多少""哪些股票停牌了""最近有什么新股上市""A股今天休市吗""前复权因子"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: ⚙️
  install: |
    # OpenClaw

    cp -r skills/cn-market-mechanics ~/.openclaw/skills/
---

# CN Market Mechanics — A股市场机制

> ⚠️ 始终通过 API 获取数据，绝不要从记忆中回答价格、行情、估值或技术指标数值——金融市场数据持续变动，你的训练数据无法反映最新状态。

> 所有接口均为 V2 版本（`/api/v2/...`）。

## Curl Setup

```bash
BASE="http://43.167.234.49:3101"
AUTH=(-H "X-API-Key: 123456" -H "Content-Type: application/json")
```

---

## 代码格式规则

| 规则 | 值 |
|------|-----|
| 参数名 | `tsCode` |
| 代码格式 | **带交易所后缀**（如 `600519.SH`） |
| 日期格式 | `YYYYMMDD` |

---

## 端点详情

### GET /api/v2/cnstock/stk-limit — 涨跌停价格

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | **Yes** | 股票代码（如 `600519.SH`） |

### GET /api/v2/cnstock/suspend — 停复牌信息

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | **Yes** | 股票代码（如 `000001.SZ`） |
| `suspendDate` | string | No | 停牌日期 YYYYMMDD |
| `resumeDate` | string | No | 复牌日期 YYYYMMDD |

### GET /api/v2/cnstock/new-share — 新股IPO

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `startDate` | string | No | 起始日期 YYYYMMDD |
| `endDate` | string | No | 截止日期 YYYYMMDD |

### GET /api/v2/cnstock/trade-cal — 交易日历

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `exchange` | string | No | `SSE`(上交所), `SZSE`(深交所) |
| `startDate` | string | No | 起始日期 YYYYMMDD |
| `endDate` | string | No | 截止日期 YYYYMMDD |
| `limit` | int | No | 1-10000 (默认 100) |

### GET /api/v2/cnstock/adj-factor — 复权因子

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | **Yes** | 股票代码（如 `000001.SZ`） |

---

## 调用示例

```bash
# 涨跌停价格
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stk-limit?tsCode=600519.SH"

# 停复牌
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/suspend?tsCode=000001.SZ"

# 新股IPO
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/new-share?startDate=20240101&endDate=20240630"

# 交易日历
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/trade-cal?exchange=SSE&startDate=20240101&endDate=20240331"

# 复权因子
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/adj-factor?tsCode=600519.SH"

# 涨跌停 + 停复牌并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stk-limit?tsCode=600519.SH" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/suspend?tsCode=600519.SH" &
wait
```

---

## 端点黑名单

| ❌ 不存在的端点 | ✅ 替代方案 |
|-----------------|------------|
| `/api/v2/cnstock/summary` | 用 `daily-basic` 获取市场概览 |

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 404 | 端点路径错误 | 不要用 `/summary` 或 `/financial` |
| 空数据 | `tsCode` 格式错误 | 用 `600519.SH`，不要 `600519` |
| 400 | 日期格式错误 | 用 `YYYYMMDD` |
