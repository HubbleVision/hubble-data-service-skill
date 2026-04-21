# 美股 API Reference

## 美股证券实时行情

### Action: Get US Securities Quote

- `GET /api/v2/usstock/securities`

批量查询美股实时报价。价格字段已按 power 动态换算，百分比字段已除以 100。单次最多 500 个代码。

美股使用 Ticker 符号（如 `AAPL`），与其他接口格式一致，无混淆风险。

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | **Yes** | 证券代码，逗号分隔（如 `AAPL,TSLA,MSFT`） |
| `fields` | string | No | 指定返回字段，逗号分隔（不传则返回全部） |

**请求示例：**
```bash
# 查询单只股票
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL"

# 批量查询
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL,TSLA,MSFT"

# 只查指定字段
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL&fields=price,chgPct,volume"
```

**响应：**
```json
{
  "success": true,
  "data": {
    "AAPL": {
      "name": "Apple Inc.",
      "price": 178.50,
      "chgPct": 0.85,
      "volume": 52000000
    }
  },
  "timestamp": 1710865200000
}
```

---

## Action: Get US Stock List

- `GET /api/v2/usstock/symbols`

No parameters required.

**Request:**
```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/symbols"
```

---

## Action: Get US Stock Daily Data

- `GET /api/v2/usstock/stocks`

**Query Parameters:**
| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `symbol` | string | **Yes** | US stock code | `AAPL` |
| `startDate` | string | No | Start date YYYYMMDD | `20240101` |
| `endDate` | string | No | End date YYYYMMDD | `20240131` |
| `limit` | int | No | Max 1-5000 (default: 100) | `500` |

**Request:**
```bash
curl -sS "${AUTH[@]}" \
  "$BASE/api/v2/usstock/stocks?symbol=AAPL&startDate=20240101&endDate=20240131&limit=500"
```

---

## Action: Get US Trade Calendar

- `GET /api/v2/usstock/trade-cal`

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `startDate` | string | No | Start date YYYYMMDD |
| `endDate` | string | No | End date YYYYMMDD |
| `limit` | int | No | Max 1-10000 (default: 1000) |

**Request:**
```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/trade-cal?startDate=20240101&endDate=20240630"
```

---

## 美股实时盘口数据

> 代码格式：**Ticker**，如 `AAPL`, `TSLA`。

| Endpoint | 参数名 | 代码格式 | 说明 |
|----------|--------|----------|------|
| `/api/v2/usstock/min` | `code` | `AAPL` | 分时数据 (min1/min5) |
| `/api/v2/usstock/brokerq` | `code` | `AAPL` | 五档买卖盘 |
| `/api/v2/usstock/orderbook` | `code` | `AAPL` | 十档买卖盘 |
| `/api/v2/usstock/tick` | `code` | `AAPL` | 逐笔成交 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/min?code=AAPL&ktype=min1"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/brokerq?code=AAPL"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/orderbook?code=AAPL"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/tick?code=AAPL&count=50"
```

---

## 美股财务数据

> 部分接口需要 Premium 订阅。

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `/api/v2/usstock/finance/income` | 利润表 | `symbol` |
| `/api/v2/usstock/finance/balancesheet` | 资产负债表 | `symbol` |
| `/api/v2/usstock/finance/cashflow` | 现金流量表 | `symbol` |
| `/api/v2/usstock/finance/dividend` | 股息历史 | `symbol`, `limit` |
| `/api/v2/usstock/finance/forecast` | 盈利预估 | `symbol`, `limit` |
| `/api/v2/usstock/finance/splits` | 拆股历史 | `symbol`, `limit` |
| `/api/v2/usstock/finance/shares` | 流通股数量 | `symbol`, `limit` |
| `/api/v2/usstock/finance/insider` | 内部人士交易 | `symbol`, `limit` |
| `/api/v2/usstock/finance/institutional` | 机构持仓 | `symbol`, `limit` |
| `/api/v2/usstock/finance/transcript` | 财报电话会议 (Premium) | `symbol`, `quarter` |
| `/api/v2/usstock/etf-profile` | ETF 档案 | `symbol`, `limit` |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/finance/income?symbol=AAPL"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/finance/dividend?symbol=AAPL"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/finance/institutional?symbol=AAPL"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/etf-profile?symbol=SPY"
```

---

## 美股市场信息

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `/api/v2/usstock/market-status` | 交易所状态 | 无 |
| `/api/v2/usstock/top-movers` | 涨跌排行 | 无 |
| `/api/v2/usstock/calendar/earnings` | 盈利日历 | `symbol`, `horizon` |
| `/api/v2/usstock/calendar/ipo` | IPO 日历 | 无 |
| `/api/v2/usstock/market-overview` | 市场全局概览 | 无 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/market-status"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/top-movers"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/calendar/earnings?symbol=AAPL&horizon=3month"
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/market-overview"
```

---

## 期权 (Premium)

| Endpoint | 说明 | 关键参数 |
|----------|------|----------|
| `/api/v2/options/chain` | 实时期权链 | `symbol`, `require_greeks`, `expiration` |
| `/api/v2/options/history` | 历史期权链 | `symbol`, `date` |
| `/api/v2/options/put-call-ratio` | Put/Call 比率 | `symbol`, `date`, `realtime` |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/options/chain?symbol=AAPL&require_greeks=true"
curl -sS "${AUTH[@]}" "$BASE/api/v2/options/put-call-ratio?symbol=AAPL&realtime=true"
```
