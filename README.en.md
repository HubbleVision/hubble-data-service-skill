<p align="center">
  <img src="Hubble_All Black.png" alt="Hubble" width="200">
</p>

<p align="center">
  <a href="README.md">中文</a> · <a href="README.en.md">English</a>
</p>

# Hubble Data Service Skill

[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet)](https://claude.ai/code)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-Compatible-green)](https://openclaw.dev)
[![npx skills](https://img.shields.io/badge/npx%20skills-installable-orange)](https://www.npmjs.com/package/skills)

> Financial market data Skills for AI Coding Agents — quotes, K-lines, technical indicators, and research reports.
> Supports Claude Code, Cursor, OpenClaw, and 41+ agents.

Covering **A-shares, HK stocks, US stocks, crypto, funds, commodities, and global indices** — 19 modular Skills + 1 intelligent router.

---

## What is this?

A collection of **Agent Skills** that let AI coding assistants (Claude Code, Cursor, OpenClaw, etc.) query real-time financial market data.

Once installed, you can talk to your agent like this:

```
> What's Moutai's price today?
> How's BTC funding rate lately?
> Analyze Apple's technicals — RSI, MACD, Bollinger Bands
> Generate a deep research report on Tesla
> What's the northbound capital flow today?
```

The agent will call the corresponding API and return real data — no hallucination.

### Data Coverage

| Market | Realtime Quotes | K-lines | Fundamentals | Highlights |
|--------|:--------------:|:-------:|:------------:|------------|
| A-shares | ✅ | ✅ | ✅ | Limit up/down, Stock Connect, Margin, Shenwan Industry, Macro |
| HK Stocks | ✅ | ✅ | ✅ | Stock Connect holdings, Top 10 |
| US Stocks | ✅ | ✅ | ✅ | Earnings calls, Options chain, Insider trading |
| Crypto | ✅ | ✅ | — | Open Interest, Liquidation, Funding Rate, Long/Short, Whale tracking, ETF |
| Cross-market | — | — | — | 27 technical indicators (RSI/MACD/KDJ/BOLL etc.) |
| Funds | — | — | ✅ | NAV, Holdings, Dividends |
| Commodities | — | ✅ | — | Gold, Oil, Natural Gas, Copper, Aluminum |
| Global Indices | — | ✅ | — | SPX, DJI, SSE, HSI, etc. |

---

## Install

### One-click Install (Recommended)

```bash
npx skills add HubbleVision/hubble-data-service-skill
```

All 19 Skills will be automatically registered to your agent (Claude Code → `.claude/skills/`, Cursor → `.agents/skills/`, etc.).

### Selective Install

```bash
# A-share K-lines only
npx skills add HubbleVision/hubble-data-service-skill --skill cn-kline

# Crypto only
npx skills add HubbleVision/hubble-data-service-skill --skill crypto-market --skill crypto-indicators

# A-share + HK realtime quotes
npx skills add HubbleVision/hubble-data-service-skill --skill cn-realtime-quote --skill hk-realtime-quote
```

### Target Specific Agent

```bash
# Claude Code only
npx skills add HubbleVision/hubble-data-service-skill -a claude-code

# Claude Code + Cursor
npx skills add HubbleVision/hubble-data-service-skill -a claude-code -a cursor

# Global install (available across all projects)
npx skills add HubbleVision/hubble-data-service-skill -g
```

> **Ready to use**: API endpoint and key are built into each Skill — no extra configuration needed after installation.

### Verify Installation

```bash
npx skills list
```

---

## Skill List

| Skill | Description | Trigger Examples |
|-------|-------------|------------------|
| `skill-router` | Intelligent router, dispatches to the right skill | Any financial data question |
| `cn-realtime-quote` | A-share realtime quotes | "Moutai price" "A-share quotes" |
| `cn-kline` | A-share K-lines | "Moutai daily K" "Vanke weekly" |
| `cn-fundamental` | A-share fundamentals & financials | "Moutai PE" "Vanke financials" |
| `cn-index` | A-share indices | "SSE index" "Shenwan industry" |
| `cn-market` | A-share market data | "IPO calendar" "trading calendar" |
| `cn-market-mechanics` | A-share market mechanics | "Limit up/down" "suspension" "block trade" |
| `hk-realtime-quote` | HK stock realtime quotes | "Tencent price" "HK quotes" |
| `hk-kline` | HK stock K-lines | "Tencent daily K" "HK K-lines" |
| `hk-fundamental` | HK stock fundamentals | "Tencent earnings" "HK company info" |
| `hk-market` | HK stock market data | "Stock Connect" "trading calendar" |
| `hk-connect` | HK Stock Connect | "Northbound flow" "Connect holdings" |
| `us-realtime-quote` | US stock realtime quotes | "AAPL price" "Tesla quote" |
| `us-kline` | US stock K-lines | "Apple daily K" "Nvidia weekly" |
| `us-fundamental` | US stock fundamentals & financials | "AAPL PE" "Tesla earnings" |
| `us-market` | US stock market data | "US IPO" "earnings calendar" |
| `crypto-market` | Crypto full market data | "BTC" "funding rate" "liquidation" |
| `crypto-indicators` | Crypto indicators | "Fear & Greed" "AHR999" |
| `cross-market-indicators` | 27 technical indicators | "RSI" "MACD" "Bollinger Bands" |

---

## Authentication

```bash
BASE="https://your-api-host"
AUTH=(-H "X-API-Key: YOUR_API_KEY" -H "Content-Type: application/json")
```

All requests require `X-API-Key` header.

---

## Quick Reference: Symbol Formats

Using the wrong format is the #1 cause of errors. Each endpoint type uses a different parameter and format.

| Data Type | Parameter | A-share | HK Stock | US Stock |
|-----------|-----------|---------|----------|----------|
| Realtime quote | `codes` | `600519` | `00700` | `AAPL` |
| K-line / OHLCV | `symbol` | `000001.SZ` | `00700.HK` | `AAPL` |
| Fundamentals | `tsCode` | `600519.SH` | `00700.HK` | `AAPL` |
| Technical indicators | `symbol` + `market` | `cn` + `000001.SZ` | `hk` + `00700.HK` | `us` + `AAPL` |
| Order book / tick | `code` | `600519` | `00700` | `AAPL` |

**Key rules:**
- Realtime quotes (`securities`): **pure digits**, no exchange suffix
- K-line & fundamentals: **with exchange suffix** (`.SZ`, `.SH`, `.HK`)
- US stocks: Ticker symbol only, no suffix ever
- Crypto: Trading pair format like `BTCUSDT`, coin like `BTC`

---

## A-Shares (`/api/v2/cnstock/...`)

### Realtime Quote

```
GET /api/v2/cnstock/securities
```

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | Yes | Comma-separated, max 500. **Pure digits only**: `600519,000001,300750` |
| `fields` | string | No | Comma-separated fields to return |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519,000001"
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519&fields=price,chgPct,volume"
```

### K-Line

```
GET /api/v2/cnstock/stocks
```

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | Yes | With suffix: `000001.SZ`, `600519.SH` |
| `interval` | string | No | `daily` (default), `weekly`, `monthly` |
| `startDate` | string | No | `YYYYMMDD` |
| `endDate` | string | No | `YYYYMMDD` |
| `limit` | int | No | 1-5000, default 100 |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/stocks?symbol=000001.SZ&interval=daily&limit=500"
```

### Fundamentals

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /cnstock/symbols` | `listStatus`: L/D/P | Stock list |
| `GET /cnstock/company` | `tsCode` | Company info |
| `GET /cnstock/daily-basic` | `tsCode`, `tradeDate` | PE/PB/turnover rate |
| `GET /cnstock/adj-factor` | `tsCode` | Adjustment factor |
| `GET /cnstock/name-change` | `tsCode` | Name change history |
| `GET /cnstock/new-share` | `startDate`, `endDate` | IPO calendar |
| `GET /cnstock/stk-limit` | `tsCode` | Price limit info |
| `GET /cnstock/suspend` | `tsCode` | Suspension info |
| `GET /cnstock/trade-cal` | `exchange`: SSE/SZSE | Trading calendar |

### Financial Statements

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /cnstock/finance/income` | `tsCode` | Income statement |
| `GET /cnstock/finance/balancesheet` | `tsCode` | Balance sheet |
| `GET /cnstock/finance/cashflow` | `tsCode` | Cash flow statement |
| `GET /cnstock/finance/indicator` | `tsCode` | Financial indicators |
| `GET /cnstock/finance/forecast` | `tsCode` | Earnings forecast |
| `GET /cnstock/finance/dividend` | `tsCode` | Dividend history |
| `GET /cnstock/finance/main-bz` | `tsCode` | Business segment breakdown |

### Shareholders

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /cnstock/holders/top10` | `tsCode` | Top 10 shareholders |
| `GET /cnstock/holders/float-top10` | `tsCode` | Top 10 float shareholders |
| `GET /cnstock/holders/number` | `tsCode` | Shareholder count |

### Money Flow

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /cnstock/moneyflow/hsgt` | `startDate`, `endDate` | Northbound capital flow |
| `GET /cnstock/moneyflow/hsgt-top10` | `tradeDate` | Top 10 northbound trades |
| `GET /cnstock/moneyflow/margin` | `startDate`, `endDate` | Margin trading summary |
| `GET /cnstock/moneyflow/stock` | `tsCode` | Individual stock money flow |

### Index Data

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /cnstock/index/basic` | `market`: SSE/SZSE | Index list |
| `GET /cnstock/index/daily` | `tsCode` | Daily data |
| `GET /cnstock/index/weekly` | `tsCode` | Weekly data |
| `GET /cnstock/index/monthly` | `tsCode` | Monthly data |
| `GET /cnstock/index/weight` | `indexCode` | Constituent weights |
| `GET /cnstock/index/classify` | `src`, `version` | Shenwan industry classification |

### Macro

| Endpoint | Description |
|----------|-------------|
| `GET /cnstock/macro/shibor` | SHIBOR rate |
| `GET /cnstock/macro/lpr` | LPR rate |
| `GET /cnstock/macro/cpi` | CPI |
| `GET /cnstock/macro/ppi` | PPI |
| `GET /cnstock/macro/gdp` | GDP |
| `GET /cnstock/macro/money` | Money supply (M0/M1/M2) |

### Order Book & Tick Data

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /cnstock/min` | `code` (pure digit), `ktype`: min1/min5 | Intraday data |
| `GET /cnstock/brokerq` | `code` (pure digit) | 5-level order book |
| `GET /cnstock/orderbook` | `code` (pure digit) | 10-level order book |
| `GET /cnstock/tick` | `code` (pure digit), `count` | Tick-by-tick trades |

### Reference Data

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /cnstock/repurchase` | `tsCode` | Share buyback |
| `GET /cnstock/block-trade` | `tsCode` | Block trades |
| `GET /cnstock/share-float` | `tsCode` | Lock-up expiry |
| `GET /cnstock/pledge` | `tsCode` | Share pledge |

---

## HK Stocks (`/api/v2/hkstock/...`)

### Realtime Quote

```
GET /api/v2/hkstock/securities
```

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | Yes | Comma-separated, max 500. **Pure digits**: `00700,09988` |
| `fields` | string | No | Comma-separated fields to return |

### K-Line

```
GET /api/v2/hkstock/stocks
```

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | Yes | With suffix: `00700.HK` |
| `startDate` | string | No | `YYYYMMDD` |
| `endDate` | string | No | `YYYYMMDD` |

**Note:** Use `startDate` + `endDate` for HK stocks. Do not rely on `limit`.

### Other HK Endpoints

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /hkstock/symbols` | `listStatus` | HK stock list |
| `GET /hkstock/trade-cal` | `startDate`, `endDate` | Trading calendar |
| `GET /hkstock/ggt-top10` | `tradeDate` | Stock Connect top 10 |
| `GET /hkstock/ggt-daily` | `tsCode`, `startDate`, `endDate` | Stock Connect daily |
| `GET /hkstock/hold` | `tsCode`, `startDate`, `endDate` | Stock Connect holdings |

### Order Book & Tick Data

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /hkstock/min` | `code` (pure digit), `ktype` | Intraday data |
| `GET /hkstock/brokerq` | `code` (pure digit) | 5-level order book |
| `GET /hkstock/orderbook` | `code` (pure digit) | 10-level order book |
| `GET /hkstock/tick` | `code` (pure digit) | Tick-by-tick trades |

---

## US Stocks (`/api/v2/usstock/...`)

### Realtime Quote

```
GET /api/v2/usstock/securities
```

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | Yes | Comma-separated tickers: `AAPL,TSLA,MSFT` |
| `fields` | string | No | Comma-separated fields to return |

### K-Line

```
GET /api/v2/usstock/stocks
```

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | Yes | Ticker: `AAPL` |
| `startDate` | string | No | `YYYYMMDD` |
| `endDate` | string | No | `YYYYMMDD` |
| `limit` | int | No | 1-5000, default 100 |

### Financial Data

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /usstock/finance/income` | `symbol` | Income statement |
| `GET /usstock/finance/balancesheet` | `symbol` | Balance sheet |
| `GET /usstock/finance/cashflow` | `symbol` | Cash flow statement |
| `GET /usstock/finance/dividend` | `symbol` | Dividend history |
| `GET /usstock/finance/forecast` | `symbol` | Earnings estimate |
| `GET /usstock/finance/insider` | `symbol` | Insider trading |
| `GET /usstock/finance/institutional` | `symbol` | Institutional holdings |
| `GET /usstock/finance/transcript` | `symbol`, `quarter` | Earnings call transcript (Premium) |

### Market Info

| Endpoint | Description |
|----------|-------------|
| `GET /usstock/symbols` | US stock list |
| `GET /usstock/trade-cal` | Trading calendar |
| `GET /usstock/market-status` | Exchange status |
| `GET /usstock/top-movers` | Top movers |
| `GET /usstock/market-overview` | Market overview |
| `GET /usstock/calendar/earnings` | Earnings calendar |
| `GET /usstock/calendar/ipo` | IPO calendar |

### Options (Premium)

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /options/chain` | `symbol`, `expiration` | Options chain |
| `GET /options/history` | `symbol`, `date` | Historical options |
| `GET /options/put-call-ratio` | `symbol` | Put/Call ratio |

### Order Book & Tick Data

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /usstock/min` | `code` (ticker), `ktype` | Intraday data |
| `GET /usstock/brokerq` | `code` (ticker) | 5-level order book |
| `GET /usstock/orderbook` | `code` (ticker) | 10-level order book |
| `GET /usstock/tick` | `code` (ticker) | Tick-by-tick trades |

---

## Crypto (`/api/v2/crypto/...`)

Supported exchanges: `binance`, `okx`, `bybit`, `bitget`, `kucoin`, `hyperliquid`.

### K-Line & Price

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/klines` | `exchange`, `symbol`, `interval` | Unified K-line |
| `GET /crypto/price` | `symbol`, `exchange` | Realtime price |
| `GET /crypto/marketcap` | `coin` | Market cap |
| `GET /crypto/futures` | `symbol`, `exchange` | Futures data |
| `GET /crypto/coins` | -- | Supported coins |
| `GET /crypto/symbols` | `exchange` | Supported pairs |
| `GET /crypto/exchanges` | -- | Exchange list |

### Open Interest

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/open-interest/list` | `symbol` | OI by exchange |
| `GET /crypto/open-interest/history` | `symbol`, `exchange` | OI history |
| `GET /crypto/open-interest/kline` | `symbol`, `exchange`, `interval` | OI K-line |
| `GET /crypto/open-interest/agg-history` | `coin` | Aggregate OI history |
| `GET /crypto/open-interest/agg-kline` | `coin`, `interval` | Aggregate OI K-line |
| `GET /crypto/open-interest/coin-realtime` | `coin` | Realtime OI |
| `GET /crypto/open-interest/oi-vs-mc` | `coin` | OI-to-marketcap ratio |

### Liquidation

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/liquidation/realtime` | `coin` | Realtime liquidation stats |
| `GET /crypto/liquidation/history` | `symbol`, `exchange` | Liquidation history |
| `GET /crypto/liquidation/agg-history` | `coin` | Aggregate liquidation history |
| `GET /crypto/liquidation/orders` | `symbol`, `exchange` | Liquidation orders |
| `GET /crypto/liquidation/map` | `symbol` | Liquidation map |
| `GET /crypto/liquidation/heatmap` | `symbol` | Liquidation heatmap |

### Funding Rate

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/funding-rate/realtime` | `symbol`, `exchange` | Realtime rate |
| `GET /crypto/funding-rate/history` | `symbol`, `exchange` | Historical rates |
| `GET /crypto/funding-rate/cumulative` | `symbol`, `exchange` | Cumulative rate |
| `GET /crypto/funding-rate/weighted` | `coin` | Weighted rate |
| `GET /crypto/funding-rate/kline` | `symbol`, `exchange`, `interval` | Rate K-line |
| `GET /crypto/funding-rate/heatmap` | -- | Rate heatmap |

### Long/Short Ratio

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/long-short/exchange` | `symbol`, `exchange` | Exchange L/S ratio |
| `GET /crypto/long-short/account` | `symbol`, `exchange` | Account L/S ratio |
| `GET /crypto/long-short/top-trader` | `symbol`, `exchange` | Top trader L/S (position) |
| `GET /crypto/long-short/top-trader-account` | `symbol`, `exchange` | Top trader L/S (accounts) |
| `GET /crypto/long-short/taker-ratio` | `coin` | Taker buy/sell ratio |
| `GET /crypto/long-short/kline` | `symbol`, `exchange`, `interval` | L/S K-line |

### CVD & Buy/Sell

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/cvd/kline` | `symbol`, `exchange`, `interval` | CVD K-line |
| `GET /crypto/buy-sell/count` | `symbol`, `exchange` | Buy/sell trade count |
| `GET /crypto/buy-sell/volume` | `symbol`, `exchange` | Buy/sell volume |
| `GET /crypto/buy-sell/amount` | `symbol`, `exchange` | Buy/sell amount |
| `GET /crypto/buy-sell/agg-count` | `coin` | Aggregate count |
| `GET /crypto/buy-sell/agg-volume` | `coin` | Aggregate volume |

### ETF

| Endpoint | Description |
|----------|-------------|
| `GET /crypto/etf/btc` | US BTC ETF data |
| `GET /crypto/etf/eth` | US ETH ETF data |
| `GET /crypto/etf/btc-flow` | BTC ETF net flow |
| `GET /crypto/etf/eth-flow` | ETH ETF net flow |

### Market Indicators

| Endpoint | Description |
|----------|-------------|
| `GET /crypto/indicator/fear-greed` | Fear & Greed Index |
| `GET /crypto/indicator/ahr999` | AHR999 indicator |
| `GET /crypto/indicator/two-year-ma` | 2-year MA multiplier |
| `GET /crypto/indicator/btc-dominance` | BTC dominance |
| `GET /crypto/indicator/puell` | Puell Multiple |
| `GET /crypto/indicator/pi-cycle` | Pi Cycle indicator |
| `GET /crypto/indicator/altcoin-index` | Altcoin Season Index |
| `GET /crypto/indicator/grayscale` | Grayscale holdings |
| `GET /crypto/rsi/screener` | RSI screener |

### News

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/news/list` | `page`, `pageSize` | News list |
| `GET /crypto/news/detail` | `id` | News detail |
| `GET /crypto/news/search` | `keyword` | Search news |
| `GET /crypto/news/latest` | `limit` | Latest news |

### Whale & Large Orders

| Endpoint | Description |
|----------|-------------|
| `GET /crypto/whale/positions` | Whale positions (HyperLiquid) |
| `GET /crypto/whale/activity` | Whale activity |
| `GET /crypto/large-order/market` | Large market orders |
| `GET /crypto/large-order/limit` | Large limit orders |

### Rankings

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/ranking/open-interest` | `period`, `exchange` | OI ranking |
| `GET /crypto/ranking/liquidation` | `period`, `exchange` | Liquidation ranking |
| `GET /crypto/ranking/price-change` | `period`, `exchange` | Price change ranking |
| `GET /crypto/ranking/volume` | `period`, `exchange` | Volume ranking |

### Order Book & Flow

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/order-book/by-symbol` | `symbol` | Order book delta by pair |
| `GET /crypto/order-book/by-exchange` | `exchange` | Order book delta by exchange |
| `GET /crypto/order-book/heatmap` | `symbol` | Liquidity heatmap |
| `GET /crypto/order-flow/lists` | `symbol` | Order flow list |

### Capital Flow

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /crypto/capital-flow/realtime` | `coin` | Realtime capital flow |
| `GET /crypto/capital-flow/history` | `coin` | Historical capital flow |
| `GET /crypto/net-positions` | `symbol` | Net long/short positions |

---

## Technical Indicators (`/api/v2/indicators/...`)

27 indicators supported: SMA, EMA, RSI, MACD, BOLL, KDJ, ADX, ATR, CCI, VWAP, OBV, NATR, MFI, WILLR, STDDEV, Aroon, TRIX, MOM, ROC, CMO, AD, HT Trendline, PPO, SAR, Stoch, ULTOSC, ADOSC.

**The `market` parameter is required** for all indicator endpoints: `cn` (A-share), `hk` (HK stock), `us` (US stock).

**Date format for indicators: `YYYY-MM-DD`** (different from other endpoints which use `YYYYMMDD`).

### Batch (Recommended)

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

**Params mapping:**

| Indicators | params format | Example |
|------------|---------------|---------|
| SMA, EMA, RSI, ATR, CCI, VWAP, NATR, MFI, WILLR, STDDEV, Aroon, TRIX | `[period]` | `[14]` |
| MOM, ROC, CMO | `[period]` | `[10]` |
| MACD | `[fast, slow, signal]` | `[12, 26, 9]` |
| BOLL | `[period, nbdev]` | `[20, 2.0]` |
| KDJ | `[fastk, slowk, slowd]` | `[9, 3, 3]` |
| PPO, ADOSC | `[fast, slow]` | `[12, 26]` |
| SAR | `[acceleration, maximum]` | `[0.02, 0.2]` |
| Stoch | `[k, d, smooth]` | `[14, 3, 3]` |
| ULTOSC | `[p1, p2, p3]` | `[7, 14, 28]` |
| OBV, AD, HT Trendline | No params | Omit |

### Single Indicator

```
GET /api/v2/indicators/{type}?market=&symbol=&interval=1d&period=&limit=100
```

```bash
# A-share RSI
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/rsi?market=cn&symbol=000001.SZ&interval=1d&period=14&limit=100"

# HK MACD
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/macd?market=hk&symbol=00700.HK&interval=1d&fast_period=12&slow_period=26&signal_period=9"

# US Bollinger
curl -sS "${AUTH[@]}" "$BASE/api/v2/indicators/boll?market=us&symbol=AAPL&interval=1d&period=20&nbdev=2.0"
```

---

## Funds (`/api/v2/fund/...`)

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /fund/basic` | `listStatus` | Fund list |
| `GET /fund/nav` | `tsCode` | NAV data |
| `GET /fund/daily` | `tsCode` | ETF daily data |
| `GET /fund/portfolio` | `tsCode` | Fund holdings |
| `GET /fund/etf-basic` | `listStatus` | ETF list |
| `GET /fund/share` | `tsCode` | Fund shares |
| `GET /fund/div` | `tsCode` | Fund dividends |

---

## Commodities (`/api/v2/commodity/...`)

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /commodity/spot` | `symbol`: GOLD/SILVER/PLATINUM/PALLADIUM | Spot price |
| `GET /commodity/klines` | `symbol`, `interval` | K-line data |
| `GET /commodity/WTI` | `interval` | WTI crude oil |
| `GET /commodity/BRENT` | `interval` | Brent crude oil |
| `GET /commodity/NATURAL_GAS` | `interval` | Natural gas |
| `GET /commodity/COPPER` | `interval` | Copper |
| `GET /commodity/ALUMINUM` | `interval` | Aluminum |

---

## Global Indices (`/api/v2/index/...`)

| Endpoint | Key Param | Description |
|----------|-----------|-------------|
| `GET /index/symbols` | -- | Index directory |
| `GET /index/klines` | `symbol`, `interval`, `limit` | Index K-line |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/index/symbols"
curl -sS "${AUTH[@]}" "$BASE/api/v2/index/klines?symbol=SPX&interval=daily&limit=100"
```

---

## Common Date Formats

| Endpoint Type | Format | Example |
|---------------|--------|---------|
| Stock K-line, fundamentals, money flow | `YYYYMMDD` | `20240115` |
| Technical indicators | `YYYY-MM-DD` | `2024-01-15` |
| Crypto K-line timestamps | Milliseconds | `1704067200000` |

---

## Error Codes

| Code | Meaning |
|------|---------|
| 400 | Invalid parameters |
| 401 | Invalid API key |
| 404 | Symbol not found |
| 500 | Internal error |

---

## Common Issues

| Symptom | Cause | Fix |
|---------|-------|-----|
| Empty data from securities | Code has exchange suffix | Use pure digits: `600519` not `600519.SH` |
| 404 on stock endpoint | Wrong code format | K-line uses `symbol` with suffix; fundamentals use `tsCode` |
| HK stock missing latest data | Using `limit` parameter | Use `startDate` + `endDate` instead |
| Indicator returns null | Insufficient warmup data | Increase `limit` |
| 400 on indicators | Wrong date format | Use `YYYY-MM-DD` not `YYYYMMDD` |
| 400 missing market | Indicator without market | Add `market=cn` / `hk` / `us` |

---

## Example: Multi-market Portfolio Check

```bash
# Parallel requests across markets
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519,000001" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700,09988" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL,TSLA" &
wait
```

## Example: Crypto Comprehensive Analysis

```bash
# Parallel: price + funding rate + OI + Fear & Greed
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/price?symbol=BTCUSDT&exchange=binance" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/funding-rate/realtime?symbol=BTCUSDT&exchange=binance" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/open-interest/coin-realtime?coin=BTC" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/crypto/indicator/fear-greed" &
wait
```

---

[中文文档](README.md)
