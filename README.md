<p align="center">
  <img src="Hubble_All Black.png" alt="Hubble" width="200">
</p>

# Hubble Data Service

金融市场数据 API，覆盖 A股、港股、美股、加密货币、基金、大宗商品及全球指数。

**所有接口均为 V2 版本（`/api/v2/...`）。**

---

## 认证

```bash
BASE="https://your-api-host"
AUTH=(-H "X-API-Key: YOUR_API_KEY" -H "Content-Type: application/json")
```

所有请求需携带 `X-API-Key` 请求头。

---

## 代码格式速查

代码格式错误是最常见的问题。不同类型的接口使用不同的参数名和格式：

| 数据类型 | 参数名 | A股 | 港股 | 美股 |
|----------|--------|-----|------|------|
| 实时行情 | `codes` | `600519` | `00700` | `AAPL` |
| K线 / OHLCV | `symbol` | `000001.SZ` | `00700.HK` | `AAPL` |
| 基础数据 | `tsCode` | `600519.SH` | `00700.HK` | `AAPL` |
| 技术指标 | `symbol` + `market` | `cn` + `000001.SZ` | `hk` + `00700.HK` | `us` + `AAPL` |
| 盘口 / 逐笔 | `code` | `600519` | `00700` | `AAPL` |

**核心规则：**
- 实时行情（`securities`）：**纯数字**，不带交易所后缀
- K线 & 基础数据：**带交易所后缀**（`.SZ`、`.SH`、`.HK`）
- 美股：始终使用 Ticker 符号，永远不带后缀
- 加密货币：交易对格式如 `BTCUSDT`，币种如 `BTC`

---

## A股 (`/api/v2/cnstock/...`)

### 实时行情

```
GET /api/v2/cnstock/securities
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `codes` | string | 是 | 逗号分隔，最多 500 个。**纯数字**：`600519,000001,300750` |
| `fields` | string | 否 | 指定返回字段，逗号分隔 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519,000001"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519&fields=price,chgPct,volume"
```

### K线数据

```
GET /api/v2/cnstock/stocks
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `symbol` | string | 是 | 带后缀：`000001.SZ`、`600519.SH` |
| `interval` | string | 否 | `daily`（默认）、`weekly`、`monthly` |
| `startDate` | string | 否 | `YYYYMMDD` |
| `endDate` | string | 否 | `YYYYMMDD` |
| `limit` | int | 否 | 1-5000，默认 100 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=000001.SZ&interval=daily&limit=500"
```

### 基础数据

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /cnstock/symbols` | `listStatus`: L/D/P | 股票列表 |
| `GET /cnstock/company` | `tsCode` | 公司信息 |
| `GET /cnstock/daily-basic` | `tsCode`、`tradeDate` | PE/PB/换手率 |
| `GET /cnstock/adj-factor` | `tsCode` | 复权因子 |
| `GET /cnstock/name-change` | `tsCode` | 更名历史 |
| `GET /cnstock/new-share` | `startDate`、`endDate` | 新股 IPO |
| `GET /cnstock/stk-limit` | `tsCode` | 涨跌停价格 |
| `GET /cnstock/suspend` | `tsCode` | 停复牌 |
| `GET /cnstock/trade-cal` | `exchange`: SSE/SZSE | 交易日历 |

### 财务数据

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /cnstock/finance/income` | `tsCode` | 利润表 |
| `GET /cnstock/finance/balancesheet` | `tsCode` | 资产负债表 |
| `GET /cnstock/finance/cashflow` | `tsCode` | 现金流量表 |
| `GET /cnstock/finance/indicator` | `tsCode` | 财务指标 |
| `GET /cnstock/finance/forecast` | `tsCode` | 业绩预告 |
| `GET /cnstock/finance/dividend` | `tsCode` | 分红送股 |
| `GET /cnstock/finance/main-bz` | `tsCode` | 主营业务构成 |

### 股东数据

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /cnstock/holders/top10` | `tsCode` | 前十大股东 |
| `GET /cnstock/holders/float-top10` | `tsCode` | 前十大流通股东 |
| `GET /cnstock/holders/number` | `tsCode` | 股东人数 |

### 资金流向

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /cnstock/moneyflow/hsgt` | `startDate`、`endDate` | 沪深港通资金流向 |
| `GET /cnstock/moneyflow/hsgt-top10` | `tradeDate` | 沪深股通十大成交股 |
| `GET /cnstock/moneyflow/margin` | `startDate`、`endDate` | 融资融券汇总 |
| `GET /cnstock/moneyflow/stock` | `tsCode` | 个股资金流向 |

### 指数数据

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /cnstock/index/basic` | `market`: SSE/SZSE | 指数列表 |
| `GET /cnstock/index/daily` | `tsCode` | 日线 |
| `GET /cnstock/index/weekly` | `tsCode` | 周线 |
| `GET /cnstock/index/monthly` | `tsCode` | 月线 |
| `GET /cnstock/index/weight` | `indexCode` | 成分股权重 |
| `GET /cnstock/index/classify` | `src`、`version` | 申万行业分类 |

### 宏观经济

| 端点 | 说明 |
|------|------|
| `GET /cnstock/macro/shibor` | Shibor 利率 |
| `GET /cnstock/macro/lpr` | LPR 利率 |
| `GET /cnstock/macro/cpi` | CPI 居民消费价格指数 |
| `GET /cnstock/macro/ppi` | PPI 工业生产者价格指数 |
| `GET /cnstock/macro/gdp` | GDP 国内生产总值 |
| `GET /cnstock/macro/money` | 货币供应量 (M0/M1/M2) |

### 盘口与逐笔

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /cnstock/min` | `code`（纯数字）、`ktype`: min1/min5 | 分时数据 |
| `GET /cnstock/brokerq` | `code`（纯数字） | 五档买卖盘 |
| `GET /cnstock/orderbook` | `code`（纯数字） | 十档买卖盘 |
| `GET /cnstock/tick` | `code`（纯数字）、`count` | 逐笔成交 |

### 参考数据

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /cnstock/repurchase` | `tsCode` | 股票回购 |
| `GET /cnstock/block-trade` | `tsCode` | 大宗交易 |
| `GET /cnstock/share-float` | `tsCode` | 限售股解禁 |
| `GET /cnstock/pledge` | `tsCode` | 股权质押 |

---

## 港股 (`/api/v2/hkstock/...`)

### 实时行情

```
GET /api/v2/hkstock/securities
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `codes` | string | 是 | 逗号分隔，最多 500 个。**纯数字**：`00700,09988` |
| `fields` | string | 否 | 指定返回字段 |

### K线数据

```
GET /api/v2/hkstock/stocks
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `symbol` | string | 是 | 带后缀：`00700.HK` |
| `startDate` | string | 否 | `YYYYMMDD` |
| `endDate` | string | 否 | `YYYYMMDD` |

**注意：** 港股 K线请使用 `startDate` + `endDate`，不要依赖 `limit` 参数。

### 其他港股端点

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /hkstock/symbols` | `listStatus` | 港股列表 |
| `GET /hkstock/trade-cal` | `startDate`、`endDate` | 交易日历 |
| `GET /hkstock/ggt-top10` | `tradeDate` | 港股通 Top10 |
| `GET /hkstock/ggt-daily` | `tsCode`、`startDate`、`endDate` | 港股通每日成交 |
| `GET /hkstock/hold` | `tsCode`、`startDate`、`endDate` | 港股通持仓 |

### 盘口与逐笔

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /hkstock/min` | `code`（纯数字）、`ktype` | 分时数据 |
| `GET /hkstock/brokerq` | `code`（纯数字） | 五档买卖盘 |
| `GET /hkstock/orderbook` | `code`（纯数字） | 十档买卖盘 |
| `GET /hkstock/tick` | `code`（纯数字） | 逐笔成交 |

---

## 美股 (`/api/v2/usstock/...`)

### 实时行情

```
GET /api/v2/usstock/securities
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `codes` | string | 是 | 逗号分隔 Ticker：`AAPL,TSLA,MSFT` |
| `fields` | string | 否 | 指定返回字段 |

### K线数据

```
GET /api/v2/usstock/stocks
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `symbol` | string | 是 | Ticker 符号：`AAPL` |
| `startDate` | string | 否 | `YYYYMMDD` |
| `endDate` | string | 否 | `YYYYMMDD` |
| `limit` | int | 否 | 1-5000，默认 100 |

### 财务数据

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /usstock/finance/income` | `symbol` | 利润表 |
| `GET /usstock/finance/balancesheet` | `symbol` | 资产负债表 |
| `GET /usstock/finance/cashflow` | `symbol` | 现金流量表 |
| `GET /usstock/finance/dividend` | `symbol` | 股息历史 |
| `GET /usstock/finance/forecast` | `symbol` | 盈利预估 |
| `GET /usstock/finance/insider` | `symbol` | 内部人士交易 |
| `GET /usstock/finance/institutional` | `symbol` | 机构持仓 |
| `GET /usstock/finance/transcript` | `symbol`、`quarter` | 财报电话会议（Premium） |

### 市场信息

| 端点 | 说明 |
|------|------|
| `GET /usstock/symbols` | 美股列表 |
| `GET /usstock/trade-cal` | 交易日历 |
| `GET /usstock/market-status` | 交易所状态 |
| `GET /usstock/top-movers` | 涨跌排行 |
| `GET /usstock/market-overview` | 市场概览 |
| `GET /usstock/calendar/earnings` | 财报日历 |
| `GET /usstock/calendar/ipo` | IPO 日历 |

### 期权（Premium）

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /options/chain` | `symbol`、`expiration` | 期权链 |
| `GET /options/history` | `symbol`、`date` | 历史期权 |
| `GET /options/put-call-ratio` | `symbol` | Put/Call 比率 |

### 盘口与逐笔

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /usstock/min` | `code`（Ticker）、`ktype` | 分时数据 |
| `GET /usstock/brokerq` | `code`（Ticker） | 五档买卖盘 |
| `GET /usstock/orderbook` | `code`（Ticker） | 十档买卖盘 |
| `GET /usstock/tick` | `code`（Ticker） | 逐笔成交 |

---

## 加密货币 (`/api/v2/crypto/...`)

支持交易所：`binance`、`okx`、`bybit`、`bitget`、`kucoin`、`hyperliquid`。

### K线与行情

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/klines` | `exchange`、`symbol`、`interval` | 统一K线 |
| `GET /crypto/price` | `symbol`、`exchange` | 实时价格 |
| `GET /crypto/marketcap` | `coin` | 市值数据 |
| `GET /crypto/futures` | `symbol`、`exchange` | 合约行情 |
| `GET /crypto/coins` | -- | 支持币种列表 |
| `GET /crypto/symbols` | `exchange` | 支持交易对列表 |
| `GET /crypto/exchanges` | -- | 交易所列表 |

### 持仓量 (Open Interest)

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/open-interest/list` | `symbol` | 按交易所持仓列表 |
| `GET /crypto/open-interest/history` | `symbol`、`exchange` | 持仓历史 |
| `GET /crypto/open-interest/kline` | `symbol`、`exchange`、`interval` | 持仓K线 |
| `GET /crypto/open-interest/agg-history` | `coin` | 币种聚合持仓历史 |
| `GET /crypto/open-interest/agg-kline` | `coin`、`interval` | 聚合持仓K线 |
| `GET /crypto/open-interest/coin-realtime` | `coin` | 实时持仓 |
| `GET /crypto/open-interest/oi-vs-mc` | `coin` | 持仓市值比 |

### 爆仓数据 (Liquidation)

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/liquidation/realtime` | `coin` | 爆仓实时统计 |
| `GET /crypto/liquidation/history` | `symbol`、`exchange` | 爆仓统计历史 |
| `GET /crypto/liquidation/agg-history` | `coin` | 币种爆仓聚合历史 |
| `GET /crypto/liquidation/orders` | `symbol`、`exchange` | 爆仓订单 |
| `GET /crypto/liquidation/map` | `symbol` | 清算地图 |
| `GET /crypto/liquidation/heatmap` | `symbol` | 清算热力图 |

### 资金费率 (Funding Rate)

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/funding-rate/realtime` | `symbol`、`exchange` | 实时资金费率 |
| `GET /crypto/funding-rate/history` | `symbol`、`exchange` | 历史资金费率 |
| `GET /crypto/funding-rate/cumulative` | `symbol`、`exchange` | 累计资金费率 |
| `GET /crypto/funding-rate/weighted` | `coin` | 加权资金费率 |
| `GET /crypto/funding-rate/kline` | `symbol`、`exchange`、`interval` | 资金费率K线 |
| `GET /crypto/funding-rate/heatmap` | -- | 资金费率热力图 |

### 多空比 (Long/Short)

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/long-short/exchange` | `symbol`、`exchange` | 交易所多空比 |
| `GET /crypto/long-short/account` | `symbol`、`exchange` | 多空持仓人数比 |
| `GET /crypto/long-short/top-trader` | `symbol`、`exchange` | 大户多空比（持仓量） |
| `GET /crypto/long-short/top-trader-account` | `symbol`、`exchange` | 大户多空比（账户数） |
| `GET /crypto/long-short/taker-ratio` | `coin` | 主动买卖多空比 |
| `GET /crypto/long-short/kline` | `symbol`、`exchange`、`interval` | 多空比K线 |

### CVD / 主动买卖

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/cvd/kline` | `symbol`、`exchange`、`interval` | CVD K线 |
| `GET /crypto/buy-sell/count` | `symbol`、`exchange` | 主动买卖笔数 |
| `GET /crypto/buy-sell/volume` | `symbol`、`exchange` | 主动买卖量 |
| `GET /crypto/buy-sell/amount` | `symbol`、`exchange` | 主动买卖额 |
| `GET /crypto/buy-sell/agg-count` | `coin` | 聚合买卖笔数 |
| `GET /crypto/buy-sell/agg-volume` | `coin` | 聚合买卖量 |

### ETF

| 端点 | 说明 |
|------|------|
| `GET /crypto/etf/btc` | 美股 BTC ETF |
| `GET /crypto/etf/eth` | 美股 ETH ETF |
| `GET /crypto/etf/btc-flow` | BTC ETF 净流入流出 |
| `GET /crypto/etf/eth-flow` | ETH ETF 净流入流出 |

### 市场指标

| 端点 | 说明 |
|------|------|
| `GET /crypto/indicator/fear-greed` | 恐惧贪婪指数 |
| `GET /crypto/indicator/ahr999` | AHR999 指标 |
| `GET /crypto/indicator/two-year-ma` | 两年MA乘数 |
| `GET /crypto/indicator/btc-dominance` | BTC 市值占比 |
| `GET /crypto/indicator/puell` | 普尔系数 |
| `GET /crypto/indicator/pi-cycle` | Pi 循环 |
| `GET /crypto/indicator/altcoin-index` | 山寨指数 |
| `GET /crypto/indicator/grayscale` | 灰度持仓 |
| `GET /crypto/rsi/screener` | RSI 选币器 |

### 新闻资讯

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/news/list` | `page`、`pageSize` | 新闻列表 |
| `GET /crypto/news/detail` | `id` | 新闻详情 |
| `GET /crypto/news/search` | `keyword` | 新闻搜索 |
| `GET /crypto/news/latest` | `limit` | 最新新闻 |

### 巨鲸追踪 & 大额订单

| 端点 | 说明 |
|------|------|
| `GET /crypto/whale/positions` | 鲸鱼持仓列表（HyperLiquid） |
| `GET /crypto/whale/activity` | 鲸鱼最新动态 |
| `GET /crypto/large-order/market` | 大额市价订单 |
| `GET /crypto/large-order/limit` | 大额限价订单 |

### 排行榜

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/ranking/open-interest` | `period`、`exchange` | 持仓排行 |
| `GET /crypto/ranking/liquidation` | `period`、`exchange` | 爆仓排行 |
| `GET /crypto/ranking/price-change` | `period`、`exchange` | 价格变化排行 |
| `GET /crypto/ranking/volume` | `period`、`exchange` | 交易量排行 |

### 订单簿 & 订单流

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/order-book/by-symbol` | `symbol` | 交易对差值列表 |
| `GET /crypto/order-book/by-exchange` | `exchange` | 交易所聚合差值 |
| `GET /crypto/order-book/heatmap` | `symbol` | 挂单流动性热力图 |
| `GET /crypto/order-flow/lists` | `symbol` | 订单流列表 |

### 资金流 & 净头寸

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /crypto/capital-flow/realtime` | `coin` | 实时资金流入流出 |
| `GET /crypto/capital-flow/history` | `coin` | 历史资金流入流出 |
| `GET /crypto/net-positions` | `symbol` | 净多头和净空头 |

---

## 技术指标 (`/api/v2/indicators/...`)

支持 27 种指标：SMA、EMA、RSI、MACD、BOLL、KDJ、ADX、ATR、CCI、VWAP、OBV、NATR、MFI、WILLR、STDDEV、Aroon、TRIX、MOM、ROC、CMO、AD、HT Trendline、PPO、SAR、Stoch、ULTOSC、ADOSC。

**`market` 参数为必填**：`cn`（A股）、`hk`（港股）、`us`（美股）。

**指标端点日期格式为 `YYYY-MM-DD`**（与其他端点的 `YYYYMMDD` 不同）。

### 批量接口（推荐）

```
POST /api/v2/indicators/batch
```

```bash
curl -sS "${AUTH[@]}" -X POST "$BASE/api/v2/indicators/batch" -d '{
  "market": "cn",
  "symbol": "000001.SZ",
  "interval": "1d",
  "limit": 100,
  "indicators": [
    {"type": "rsi", "params": [14]},
    {"type": "macd", "params": [12, 26, 9]},
    {"type": "boll", "params": [20, 2.0]},
    {"type": "kdj", "params": [9, 3, 3]},
    {"type": "sma", "params": [20]}
  ]
}'
```

**参数映射：**

| 指标 | params 格式 | 示例 |
|------|-------------|------|
| SMA、EMA、RSI、ATR、CCI、VWAP、NATR、MFI、WILLR、STDDEV、Aroon、TRIX | `[period]` | `[14]` |
| MOM、ROC、CMO | `[period]` | `[10]` |
| MACD | `[fast, slow, signal]` | `[12, 26, 9]` |
| BOLL | `[period, nbdev]` | `[20, 2.0]` |
| KDJ | `[fastk, slowk, slowd]` | `[9, 3, 3]` |
| PPO、ADOSC | `[fast, slow]` | `[12, 26]` |
| SAR | `[acceleration, maximum]` | `[0.02, 0.2]` |
| Stoch | `[k, d, smooth]` | `[14, 3, 3]` |
| ULTOSC | `[p1, p2, p3]` | `[7, 14, 28]` |
| OBV、AD、HT Trendline | 无参数 | 省略 |

### 单指标接口

```
GET /api/v2/indicators/{type}?market=&symbol=&interval=1d&period=&limit=100
```

```bash
# A股 RSI
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/rsi?market=cn&symbol=000001.SZ&interval=1d&period=14&limit=100"

# 港股 MACD
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/macd?market=hk&symbol=00700.HK&interval=1d&fast_period=12&slow_period=26&signal_period=9"

# 美股布林带
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/boll?market=us&symbol=AAPL&interval=1d&period=20&nbdev=2.0"
```

---

## 基金 (`/api/v2/fund/...`)

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /fund/basic` | `listStatus` | 基金列表 |
| `GET /fund/nav` | `tsCode` | 基金净值 |
| `GET /fund/daily` | `tsCode` | ETF 日线行情 |
| `GET /fund/portfolio` | `tsCode` | 基金持仓 |
| `GET /fund/etf-basic` | `listStatus` | ETF 列表 |
| `GET /fund/share` | `tsCode` | 基金份额 |
| `GET /fund/div` | `tsCode` | 基金分红 |

---

## 大宗商品 (`/api/v2/commodity/...`)

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /commodity/spot` | `symbol`: GOLD/SILVER/PLATINUM/PALLADIUM | 贵金属现货价格 |
| `GET /commodity/klines` | `symbol`、`interval` | 商品K线 |
| `GET /commodity/WTI` | `interval` | WTI 原油 |
| `GET /commodity/BRENT` | `interval` | 布伦特原油 |
| `GET /commodity/NATURAL_GAS` | `interval` | 天然气 |
| `GET /commodity/COPPER` | `interval` | 铜 |
| `GET /commodity/ALUMINUM` | `interval` | 铝 |

---

## 全球指数 (`/api/v2/index/...`)

| 端点 | 关键参数 | 说明 |
|------|----------|------|
| `GET /index/symbols` | -- | 指数目录 |
| `GET /index/klines` | `symbol`、`interval`、`limit` | 指数K线 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/index/symbols"
curl -sS "${AUTH[@]}" "$BASE/api/v2/index/klines?symbol=SPX&interval=daily&limit=100"
```

---

## 日期格式

| 接口类型 | 格式 | 示例 |
|----------|------|------|
| 股票K线、基础数据、资金流向 | `YYYYMMDD` | `20240115` |
| 技术指标 | `YYYY-MM-DD` | `2024-01-15` |
| 加密货币K线时间戳 | 毫秒 | `1704067200000` |

---

## 错误码

| 状态码 | 含义 |
|--------|------|
| 400 | 参数错误 |
| 401 | API Key 无效 |
| 404 | 标的不存在 |
| 500 | 内部错误 |

---

## 常见问题

| 现象 | 原因 | 解决方法 |
|------|------|----------|
| 实时行情返回空数据 | `codes` 带了交易所后缀 | 用纯数字：`600519`，不要用 `600519.SH` |
| 股票端点 404 | 代码格式不对 | K线用 `symbol`（带后缀）；基础数据用 `tsCode` |
| 港股缺少最新数据 | 使用了 `limit` 参数 | 改用 `startDate` + `endDate` |
| 技术指标返回 null | 预热数据不足 | 增大 `limit` |
| 指标端点 400 | 日期格式错误 | 指标用 `YYYY-MM-DD`，不是 `YYYYMMDD` |
| 400 missing market | 未传 `market` 参数 | 必填：`cn` / `hk` / `us` |

---

## 示例：跨市场持仓查询

```bash
# 并行请求 A股、港股、美股行情
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519,000001" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700,09988" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL,TSLA" &
wait
```

## 示例：加密货币综合分析

```bash
# 并行获取：价格 + 资金费率 + 持仓量 + 恐惧贪婪指数
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/price?symbol=BTCUSDT&exchange=binance" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/funding-rate/realtime?symbol=BTCUSDT&exchange=binance" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/open-interest/coin-realtime?coin=BTC" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicator/fear-greed" &
wait
```

---

[English](README.en.md)
