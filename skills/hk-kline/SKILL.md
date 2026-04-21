---
name: hk-kline
description: >
  港股K线数据查询。获取日线、周线、月线行情。
  当用户询问"腾讯日K""港股K线""港交所周线""恒指月线"时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🇭🇰
  install: |
    # OpenClaw

    cp -r skills/hk-kline ~/.openclaw/skills/
---

# HK Kline — 港股K线数据

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
| 参数名 | `symbol` |
| 代码格式 | **带 `.HK` 后缀** |
| 日期格式 | `YYYYMMDD` |

| 示例 | 说明 |
|------|------|
| `00700.HK` | 腾讯控股 |
| `09988.HK` | 阿里巴巴 |

**❌ 错误写法**：`00700`（纯数字）

---

## ⚠️ 已知问题：`limit` 参数不返回最新数据

`/api/v2/hkstock/stocks` 的 `limit` 可能不包含最新交易日。**必须用 `startDate` + `endDate` 代替 `limit`。**

---

## 端点详情

### GET /api/v2/hkstock/stocks

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `symbol` | string | **Yes** | 股票代码（带 .HK） | `00700.HK` |
| `startDate` | string | **推荐** | YYYYMMDD | `20260301` |
| `endDate` | string | **推荐** | YYYYMMDD | `20260405` |
| `limit` | int | 不推荐 | 有 bug，可能缺少最新数据 | — |

---

## 调用示例

```bash
# ✅ 推荐：用日期范围
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/stocks?symbol=00700.HK&startDate=20260301&endDate=20260405"

# ❌ 不推荐：用 limit（可能缺少最新数据）
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/stocks?symbol=00700.HK&limit=10"

# 多股票K线并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/stocks?symbol=00700.HK&startDate=20260301&endDate=20260405" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/stocks?symbol=09988.HK&startDate=20260301&endDate=20260405" &
wait
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 404 | symbol 格式错误 | 用 `00700.HK`，不要 `00700` |
| 最新数据缺失 | 使用了 `limit` | 改用 `startDate` + `endDate` |
| 400 | 日期格式错误 | 用 `YYYYMMDD` |

---

## 端点黑名单

| ❌ 不存在的端点 | ✅ 替代方案 |
|-----------------|------------|
| `/api/v2/hkstock/daily` | `/api/v2/hkstock/stocks`（参数名 `symbol`） |
