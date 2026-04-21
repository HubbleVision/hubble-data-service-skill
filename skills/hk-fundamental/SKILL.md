---
name: hk-fundamental
description: >
  港股基础数据查询。港股列表、交易日历。
  当用户询问"港股列表""港股交易日""港股代码查询"时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📋
  install: |
    # OpenClaw

    cp -r skills/hk-fundamental ~/.openclaw/skills/
---

# HK Fundamental — 港股基础数据

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
