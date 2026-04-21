# A股 & 指数 API Reference

> 代码格式规则、日期格式、并行调用模板见 `SKILL.md` Critical Rules 部分。本文档仅补充各端点的详细参数。

## A股证券实时行情

### Get A股 Securities Quote
`GET /api/v2/cnstock/securities`

批量查询实时报价，单次最多 500 个代码。**代码格式：纯数字，不带交易所后缀。**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | **Yes** | 证券代码，逗号分隔（如 `600519,000001,300750`） |
| `fields` | string | No | 返回字段，逗号分隔（不传返回全部） |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519,000001,300750"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519&fields=price,chgPct,volume"
```

```json
{"success": true, "data": {"600519": {"name": "贵州茅台", "price": 1688.50, "chgPct": 1.25, "volume": 3500000, "high": 1695.00, "low": 1675.00, "open": 1680.00}}, "timestamp": 1710865200000}
```

---

## A股 K线数据

### Get A股 K-line
`GET /api/v2/cnstock/stocks`

| Parameter | Type | Required | Description | Example |
|-----------|------|----------|-------------|---------|
| `symbol` | string | **Yes** | 股票代码（带后缀） | `000001.SZ` |
| `interval` | string | No | `daily` (默认), `weekly`, `monthly` | `daily` |
| `startDate` | string | No | YYYYMMDD | `20240101` |
| `endDate` | string | No | YYYYMMDD | `20240131` |
| `limit` | int | No | 1-5000 (默认 100) | `500` |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=000001.SZ&interval=daily&limit=500"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=000001.SZ&interval=weekly&startDate=20240101&endDate=20240131"
```

```json
{"symbol": "000001.SZ", "interval": "daily", "data": [{"time": 1704067200000, "open": 10.50, "high": 10.80, "low": 10.40, "close": 10.70, "volume": 1234567, "adjFactor": 1.0}], "total": 500}
```

---

## A股基础数据 Endpoints

> **通用参数**：`tsCode`(带后缀), `tradeDate`, `startDate`, `endDate`, `limit`(1-5000, 默认100)。
> 代码格式规则见 SKILL.md Critical Rules 1。

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/cnstock/symbols` | — | `listStatus`: L/D/P | 股票列表 |
| `/api/v2/cnstock/company` | `tsCode` | `exchange`: SSE/SZSE | 公司信息 |
| `/api/v2/cnstock/daily-basic` | `tsCode` | — | 每日指标 (PE/PB/换手率) |
| `/api/v2/cnstock/adj-factor` | `tsCode` | — | 复权因子 |
| `/api/v2/cnstock/name-change` | `tsCode` | — | 更名历史 |
| `/api/v2/cnstock/new-share` | — | — | IPO 新股 |
| `/api/v2/cnstock/stk-limit` | `tsCode` | — | 涨跌停价格 |
| `/api/v2/cnstock/suspend` | `tsCode` | `suspendDate`, `resumeDate` | 停复牌信息 |
| `/api/v2/cnstock/trade-cal` | — | `exchange`: SSE/SZSE | 交易日历 (limit 最大 10000) |

```bash
# tsCode 类
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/company?tsCode=000001.SZ"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/daily-basic?tsCode=000001.SZ&tradeDate=20240115"

# 无代码类
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/symbols?listStatus=L"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/new-share?startDate=20240101&endDate=20240630"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/trade-cal?exchange=SSE&startDate=20240101&endDate=20240331"
```

---

## 指数数据

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/cnstock/index/basic` | — | `market`: SSE/SZSE | 指数列表 |
| `/api/v2/cnstock/index/daily` | `tsCode` (必填) | startDate, endDate, limit | 日线 |
| `/api/v2/cnstock/index/weekly` | `tsCode` (必填) | 同上 | 周线 |
| `/api/v2/cnstock/index/monthly` | `tsCode` (必填) | 同上 | 月线 |
| `/api/v2/cnstock/index/daily-basic` | `tsCode` | tradeDate, startDate, endDate, limit | 指数每日指标 |
| `/api/v2/cnstock/index/weight` | `indexCode` | tradeDate, startDate, endDate, limit | 成分股权重 |
| `/api/v2/cnstock/index/classify` | — | `src`, `version`, limit | 申万行业分类 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/index/basic?market=SSE"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/index/daily?tsCode=000001.SH&limit=100"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/index/weight?indexCode=000001.SH&limit=100"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/index/classify"
```

---

## 财务数据

> **通用参数**：`tsCode`(带后缀), `startDate`, `endDate`, `limit`(1-5000, 默认100)。

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/cnstock/finance/income` | `tsCode` | period(年报/中报/季报) | 利润表 |
| `/api/v2/cnstock/finance/balancesheet` | `tsCode` | period | 资产负债表 |
| `/api/v2/cnstock/finance/cashflow` | `tsCode` | period | 现金流量表 |
| `/api/v2/cnstock/finance/indicator` | `tsCode` | — | 财务指标 |
| `/api/v2/cnstock/finance/forecast` | `tsCode` | — | 业绩预告 |
| `/api/v2/cnstock/finance/express` | `tsCode` | — | 业绩快报 |
| `/api/v2/cnstock/finance/dividend` | `tsCode` | — | 分红送股 |
| `/api/v2/cnstock/finance/disclosure-date` | `tsCode` | — | 财报披露日期 |
| `/api/v2/cnstock/finance/main-bz` | `tsCode` | — | 主营业务构成 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/finance/income?tsCode=600519.SH"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/finance/indicator?tsCode=600519.SH"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/finance/dividend?tsCode=600519.SH"
```

---

## 股东数据

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/cnstock/holders/top10` | `tsCode` | `startDate`, `endDate`, `limit` | 前十大股东 |
| `/api/v2/cnstock/holders/float-top10` | `tsCode` | `startDate`, `endDate`, `limit` | 前十大流通股东 |
| `/api/v2/cnstock/holders/number` | `tsCode` | `startDate`, `endDate`, `limit` | 股东人数 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/holders/top10?tsCode=600519.SH&limit=5"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/holders/number?tsCode=600519.SH&limit=10"
```

---

## 参考数据

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/cnstock/repurchase` | `tsCode` | `startDate`, `endDate`, `limit` | 股票回购 |
| `/api/v2/cnstock/block-trade` | `tsCode` | `startDate`, `endDate`, `limit` | 大宗交易 |
| `/api/v2/cnstock/share-float` | `tsCode` | `startDate`, `endDate`, `limit` | 限售股解禁 |
| `/api/v2/cnstock/pledge` | `tsCode` | `startDate`, `endDate`, `limit` | 股权质押 |
| `/api/v2/cnstock/pledge-stat` | `tsCode` | `startDate`, `endDate`, `limit` | 股权质押统计 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/block-trade?startDate=20240101&endDate=20240331"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/pledge-stat?tsCode=600519.SH"
```

---

## 资金流向

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/cnstock/moneyflow/hsgt` | — | `startDate`, `endDate`, `limit` | 沪深港通资金流向 |
| `/api/v2/cnstock/moneyflow/hsgt-top10` | `tsCode` | `tradeDate` | 沪深股通十大成交股 |
| `/api/v2/cnstock/moneyflow/margin` | — | `startDate`, `endDate`, `limit` | 融资融券汇总 |
| `/api/v2/cnstock/moneyflow/margin-detail` | `tsCode` | `startDate`, `endDate`, `limit` | 融资融券明细 |
| `/api/v2/cnstock/moneyflow/stock` | `tsCode` | `startDate`, `endDate`, `limit` | 个股资金流向 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/moneyflow/hsgt?startDate=20240101&limit=30"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/moneyflow/hsgt-top10?tradeDate=20240115"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/moneyflow/stock?tsCode=600519.SH&limit=30"
```

---

## 宏观经济

| Endpoint | 额外参数 | 说明 |
|----------|----------|------|
| `/api/v2/cnstock/macro/shibor` | `startDate`, `endDate`, `limit` | Shibor 利率 |
| `/api/v2/cnstock/macro/lpr` | `startDate`, `endDate`, `limit` | LPR 利率 |
| `/api/v2/cnstock/macro/cpi` | `startDate`, `endDate`, `limit` | CPI 居民消费价格指数 |
| `/api/v2/cnstock/macro/ppi` | `startDate`, `endDate`, `limit` | PPI 工业生产者价格指数 |
| `/api/v2/cnstock/macro/gdp` | `startDate`, `endDate`, `limit` | GDP 国内生产总值 |
| `/api/v2/cnstock/macro/money` | `startDate`, `endDate`, `limit` | 货币供应量 (M0/M1/M2) |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/macro/shibor?limit=30"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/macro/lpr?limit=30"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/macro/cpi?limit=30"
```

---

## 实时盘口数据

> 返回实时盘口数据。代码格式：**纯数字，不带后缀**。

| Endpoint | 参数名 | 代码格式 | 说明 |
|----------|--------|----------|------|
| `/api/v2/cnstock/securities` | `codes` | `600519` | 证券实时行情 |
| `/api/v2/cnstock/min` | `code` | `600519` | 分时数据 (min1/min5) |
| `/api/v2/cnstock/brokerq` | `code` | `600519` | 五档买卖盘 |
| `/api/v2/cnstock/orderbook` | `code` | `600519` | 十档买卖盘 |
| `/api/v2/cnstock/tick` | `code` | `600519` | 逐笔成交 |

```bash
# 分时数据
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/min?code=600519&ktype=min1"

# 五档买卖盘
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/brokerq?code=600519"

# 十档买卖盘
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/orderbook?code=600519"

# 逐笔成交
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/tick?code=600519&count=50"
```
