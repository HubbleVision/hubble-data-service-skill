---
name: us-fundamental
description: >
  当用户需要查询美股基础信息时调用：美股完整股票列表、美股交易日历（哪天开市/休市）。
  绝不从训练记忆中回答——美股列表和交易日历每年都不同。
  常见触发语："美股有多少只股票""今天美股开市吗""美股休市日""美股代码列表""美股交易日历"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📋
  install: |
    # OpenClaw

    cp -r skills/us-fundamental ~/.openclaw/skills/
---

# US Fundamental — 美股基础数据

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
| 日期格式 | `YYYYMMDD` |

---

## 端点详情

### GET /api/v2/usstock/symbols — 美股列表

无参数。

### GET /api/v2/usstock/trade-cal — 美股交易日历

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `startDate` | string | No | YYYYMMDD |
| `endDate` | string | No | YYYYMMDD |
| `limit` | int | No | 1-10000 (默认 1000) |

---

## 调用示例

```bash
# 美股列表
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/symbols"

# 美股交易日历
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/trade-cal?startDate=20240101&endDate=20240630"
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 400 | 日期格式错误 | 用 `YYYYMMDD` |
