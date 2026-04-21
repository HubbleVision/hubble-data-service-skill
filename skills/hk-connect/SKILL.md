---
name: hk-connect
description: >
  港股通数据查询。港股通Top10、每日成交、持仓数据。
  当用户询问"港股通Top10""北向资金""南向持仓""港股通成交"时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🌉
  install: |
    # OpenClaw

    cp -r skills/hk-connect ~/.openclaw/skills/
---

# HK Connect — 港股通

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
| 代码格式 | **A股代码格式，带交易所后缀** |
| 日期格式 | `YYYYMMDD` |

| 示例 | 说明 |
|------|------|
| `600519.SH` | 贵州茅台（上交所） |
| `000001.SZ` | 平安银行（深交所） |

---

## 端点详情

### GET /api/v2/hkstock/ggt-top10 — 港股通 Top10

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | No | A股代码（如 `600519.SH`） |
| `tradeDate` | string | No | 交易日期 YYYYMMDD |
| `startDate` | string | No | 起始日期 YYYYMMDD |
| `endDate` | string | No | 截止日期 YYYYMMDD |
| `limit` | int | No | 1-5000 (默认 100) |

### GET /api/v2/hkstock/ggt-daily — 港股通每日成交

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | No | A股代码（如 `600519.SH`） |
| `tradeDate` | string | No | 交易日期 YYYYMMDD |
| `startDate` | string | No | 起始日期 YYYYMMDD |
| `endDate` | string | No | 截止日期 YYYYMMDD |
| `limit` | int | No | 1-5000 (默认 100) |

### GET /api/v2/hkstock/hold — 港股通持仓

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | No | A股代码（如 `600519.SH`） |
| `tradeDate` | string | No | 交易日期 YYYYMMDD |
| `startDate` | string | No | 起始日期 YYYYMMDD |
| `endDate` | string | No | 截止日期 YYYYMMDD |
| `limit` | int | No | 1-5000 (默认 100) |

---

## 调用示例

```bash
# 港股通 Top10（某日）
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-top10?tradeDate=20240115"

# 港股通 Top10（某股）
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-top10?tsCode=600519.SH"

# 港股通每日成交
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-daily?startDate=20240101&endDate=20240131"

# 港股通持仓
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/hold?tsCode=600519.SH"

# Top10 + 持仓并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-top10?tsCode=600519.SH" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/hold?tsCode=600519.SH" &
wait
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 空数据 | `tsCode` 用了港股格式 | 港股通用 **A股代码格式**（如 `600519.SH`），不是 `00700.HK` |
| 400 | 日期格式错误 | 用 `YYYYMMDD` |
