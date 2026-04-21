---
name: us-kline
description: >
  美股K线数据查询。获取日线行情。
  当用户询问"苹果日K""特斯拉周线""美股K线走势"时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📈
  install: |
    # OpenClaw

    cp -r skills/us-kline ~/.openclaw/skills/
---

# US Kline — 美股K线数据

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
| 代码格式 | **Ticker 符号**（如 `AAPL`） |
| 日期格式 | `YYYYMMDD` |

---

## 端点详情

### GET /api/v2/usstock/stocks

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `symbol` | string | **Yes** | Ticker 符号 | `AAPL` |
| `startDate` | string | No | YYYYMMDD | `20240101` |
| `endDate` | string | No | YYYYMMDD | `20240131` |
| `limit` | int | No | 1-5000 (默认 100) | `500` |

---

## 调用示例

```bash
# 最近100根日线（默认）
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=AAPL"

# 指定区间
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=AAPL&startDate=20240101&endDate=20240131"

# 指定数量
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=TSLA&limit=200"

# 多股票K线并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=AAPL&limit=10" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/stocks?symbol=TSLA&limit=10" &
wait
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 404 | Ticker 符号错误 | 检查大小写和拼写 |
| 空数据 | 日期区间无交易日 | 检查日期范围 |
| 400 | 日期格式错误 | 用 `YYYYMMDD` |
