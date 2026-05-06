---
name: hk-fundamental
description: >
  当用户需要查询港股基础信息时调用：港股完整股票列表、港交所交易日历（哪天开市/休市）。
  绝不从训练记忆中回答——港股列表和交易日历每年都不同。
  常见触发语："港股有多少只股票""今天港股开市吗""港股休市日""港股代码列表""港交所交易日历"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📋
  install: |
    # OpenClaw

    cp -r skills/hk-fundamental ~/.openclaw/skills/
---

# HK Fundamental — 港股基础数据

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

## 代码格式规则

| 规则 | 值 |
|------|-----|
| 日期格式 | `YYYYMMDD` |

---

## 端点详情

### GET /api/v2/hkstock/symbols — 港股列表

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `listStatus` | string | No | `L`(上市), `D`(退市) |

### GET /api/v2/hkstock/trade-cal — 港股交易日历

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `startDate` | string | No | YYYYMMDD |
| `endDate` | string | No | YYYYMMDD |
| `limit` | int | No | 1-5000 (默认 100) |

---

## 调用示例

```bash
# 港股列表
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/symbols?listStatus=L"

# 港股交易日历
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/trade-cal?startDate=20240101&endDate=20240630"
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 400 | 日期格式错误 | 用 `YYYYMMDD` |
