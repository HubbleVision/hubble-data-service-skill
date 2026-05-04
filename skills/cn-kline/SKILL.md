---
name: cn-kline
description: >
  当用户要求查看任何A股的K线图、历史走势、日线/周线/月线行情时必须调用。
  覆盖沪深京三市所有股票代码（600519, 000001, 300750...），提供开盘价、收盘价、最高价、
  最低价、成交量、成交额。绝不从训练记忆中构造K线数据——历史行情可能因复权而变化。
  常见触发语："茅台最近一个月日K""宁德时代周线走势""比亚迪月线图""大盘历史行情"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📈
  install: |
    # OpenClaw

    cp -r skills/cn-kline ~/.openclaw/skills/
---

# CN Kline — A股K线数据

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
| 参数名 | `symbol` |
| 代码格式 | **带交易所后缀** |
| 日期格式 | `YYYYMMDD` |

| 市场 | 示例 |
|------|------|
| 上交所 (SH) | `600519.SH` |
| 深交所 (SZ) | `000001.SZ` |
| 北交所 (BJ) | `430047.BJ` |

**❌ 错误写法**：`600519`（纯数字）、`SH600519`

---

## 端点详情

### GET /api/v2/cnstock/stocks

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `symbol` | string | **Yes** | 股票代码（带后缀） | `000001.SZ` |
| `interval` | string | No | `daily` (默认), `weekly`, `monthly` | `daily` |
| `startDate` | string | No | YYYYMMDD | `20240101` |
| `endDate` | string | No | YYYYMMDD | `20240131` |
| `limit` | int | No | 1-5000 (默认 100) | `500` |

---

## 调用示例

```bash
# 最近100根日线（默认）
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=000001.SZ"

# 指定区间
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=000001.SZ&startDate=20240101&endDate=20240131"

# 周线
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=600519.SH&interval=weekly&limit=50"

# 月线
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=300750.SZ&interval=monthly&limit=24"

# 多股票K线并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=002594.SZ&limit=10" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=300750.SZ&limit=10" &
wait
```

**响应示例**：

```json
{
  "symbol": "000001.SZ",
  "interval": "daily",
  "data": [
    {"time": 1704067200000, "open": 10.50, "high": 10.80, "low": 10.40, "close": 10.70, "volume": 1234567, "adjFactor": 1.0}
  ],
  "total": 500
}
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 404 | symbol 格式错误 | 用 `600519.SH`，不要 `600519` |
| 空数据 | 日期区间无交易日 | 检查日期范围是否合理 |
| 400 | 日期格式错误 | 用 `YYYYMMDD`，如 `20240101` |
