# 港股 API Reference

> 代码格式规则、日期格式、调用模板见 `SKILL.md` Critical Rules 部分。本文档仅补充各端点的详细参数。

## 港股证券实时行情

### Get HK Securities Quote
`GET /api/v2/hkstock/securities`

批量查询港股实时报价，单次最多 500 个代码。**代码格式：纯数字，不带 `.HK` 后缀。**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | **Yes** | 证券代码，逗号分隔（如 `00700,09988,09888`） |
| `fields` | string | No | 返回字段，逗号分隔 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700,09988,09888"
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700&fields=price,chgPct,volume"
```

```json
{"success": true, "data": {"00700": {"name": "腾讯控股", "price": 375.20, "chgPct": -0.53, "volume": 12000000}}, "timestamp": 1710865200000}
```

### ⚠️ Known Issue: `limit` 参数不返回最新数据

`/api/v2/hkstock/stocks` 的 `limit` 可能不包含最新交易日。**改用 `startDate` + `endDate`。**

---

## 港股 Endpoints

> **通用参数**：`tsCode`(带后缀), `tradeDate`, `startDate`, `endDate`, `limit`(1-5000, 默认100)。
> 代码格式规则见 SKILL.md Critical Rules 1。

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/hkstock/symbols` | — | `listStatus`: L/D | 股票列表 |
| `/api/v2/hkstock/stocks` | `symbol` (必填) | startDate, endDate, limit | K线 (⚠️ limit bug，用日期) |
| `/api/v2/hkstock/trade-cal` | — | startDate, endDate, limit | 交易日历 |
| `/api/v2/hkstock/ggt-top10` | `tsCode` | tradeDate, startDate, endDate, limit | 港股通 Top10 |
| `/api/v2/hkstock/ggt-daily` | `tsCode` | tradeDate, startDate, endDate, limit | 港股通每日成交 |
| `/api/v2/hkstock/hold` | `tsCode` | tradeDate, startDate, endDate, limit | 港股通持仓 |

```bash
# K线 (⚠️ 用 startDate+endDate，不用 limit)
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/stocks?symbol=00700.HK&startDate=20260301&endDate=20260405"

# 港股通
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-top10?tradeDate=20240115"
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/ggt-daily?startDate=20240101&endDate=20240131"
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/hold?tsCode=600519.SH"
```

---

## 港股实时盘口数据

> 代码格式：**纯数字，不带 `.HK` 后缀**。

| Endpoint | 参数名 | 代码格式 | 说明 |
|----------|--------|----------|------|
| `/api/v2/hkstock/securities` | `codes` | `00700` | 证券实时行情 |
| `/api/v2/hkstock/min` | `code` | `00700` | 分时数据 (min1/min5) |
| `/api/v2/hkstock/brokerq` | `code` | `00700` | 五档买卖盘 |
| `/api/v2/hkstock/orderbook` | `code` | `00700` | 十档买卖盘 |
| `/api/v2/hkstock/tick` | `code` | `00700` | 逐笔成交 |

```bash
# 分时数据
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/min?code=00700&ktype=min1"

# 五档买卖盘
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/brokerq?code=00700"

# 十档买卖盘
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/orderbook?code=00700"

# 逐笔成交
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/tick?code=00700&count=50"
```
