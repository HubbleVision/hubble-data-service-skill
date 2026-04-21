---
name: us-fundamental
description: >
  美股基础数据查询。美股列表、交易日历。
  当用户询问"美股列表""美股交易日""美股代码查询"时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📋
  install: |
    # OpenClaw

    cp -r skills/us-fundamental ~/.openclaw/skills/
---

# US Fundamental — 美股基础数据

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
