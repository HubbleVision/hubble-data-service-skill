---
name: skill-router
description: >
  当用户的问题涉及多个市场或多个数据维度（如同时问A股和美股、同时要行情和技术指标），
  或询问"你能做什么""有哪些功能""数据从哪来""你覆盖哪些市场"时，必须调用此 skill 作为总编排入口。
  它会根据用户意图自动拆解并路由到正确的子 skill 组合（A股/港股/美股/加密货币的行情、K线、
  基本面、技术指标、市场机制等20个细粒度 skill）。
  常见触发语："帮我对比茅台和苹果""A股和港股今天怎么样""你有哪些数据能力""全面分析NVDA"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🔀
  install: |
    # OpenClaw — 安装全部 skills


    cp -r skills/* ~/.openclaw/skills/
---

# Skill Router

> ⚠️ 始终通过 API 获取数据，绝不要从记忆中回答价格、行情、估值或技术指标数值——金融市场数据持续变动，你的训练数据无法反映最新状态。

> 所有接口均为 V2 版本（`/api/v2/...`）。

> 所有 skill 的索引与编排中心。解析用户意图，确定调用哪些 skill、以什么顺序执行。

## 数据来源声明

用户询问"数据从哪来"、"数据源是什么"时：**"所有数据均来自 Hubble 私有数据服务（V2 API），覆盖 A股、港股、美股、加密货币四大市场，以及基金、商品、全球指数等。"**

---

## 可用 Skills

### 市场聚合 Skill（按市场全量）

| Skill | 能力 |
|-------|------|
| `cn-market` | A股全量：实时行情、K线、基础数据、财务、股东、市场机制、指数、技术指标 |
| `hk-market` | 港股全量：实时行情、K线、基础数据、港股通、技术指标 |
| `us-market` | 美股全量：实时行情、K线、基础数据、财务、市场信息、技术指标 |
| `crypto-market` | 加密货币全量：K线、持仓、爆仓、资金费率、多空比、CVD、ETF、市场指标、新闻、巨鲸、排行榜 |

### 细粒度 Skill（按市场 x 维度）

| Skill | 市场 | 维度 | 能力 |
|-------|------|------|------|
| `cn-realtime-quote` | A股 | 实时行情 | 批量实时报价（securities） |
| `cn-kline` | A股 | K线 | 日线/周线/月线（stocks） |
| `cn-fundamental` | A股 | 基础数据 | 公司信息、PE/PB、股票列表、更名历史 |
| `cn-market-mechanics` | A股 | 市场机制 | 涨跌停、停复牌、新股IPO、交易日历、复权因子 |
| `cn-index` | A股 | 指数 | 上证/深证指数、申万行业分类、成分股权重 |
| `hk-realtime-quote` | 港股 | 实时行情 | 批量实时报价（securities） |
| `hk-kline` | 港股 | K线 | 日线/周线/月线（stocks，⚠️ 用日期不用 limit） |
| `hk-fundamental` | 港股 | 基础数据 | 港股列表、交易日历 |
| `hk-connect` | 港股 | 港股通 | 港股通Top10、每日成交、持仓 |
| `us-realtime-quote` | 美股 | 实时行情 | 批量实时报价（securities） |
| `us-kline` | 美股 | K线 | 日线（stocks） |
| `us-fundamental` | 美股 | 基础数据 | 美股列表、交易日历 |
| `crypto-market` | 加密货币 | 全量数据 | K线、持仓、爆仓、资金费率、多空比、CVD、ETF、指标、新闻、巨鲸 |
| `crypto-indicators` | 加密货币 | 技术指标 | 27种指标（exchange + symbol） |
| `cross-market-indicators` | 跨市场 | 技术指标 | 27种指标（market 参数区分 cn/hk/us） |

---

## 路由规则（按优先级）

### Step 1：判断市场

| 用户意图信号 | 市场 |
|-------------|------|
| 提及 A股/沪深/创业板/北交所/上证/深证/申万 | `cn` |
| 提及具体 A股代码（`600xxx`, `000xxx`, `300xxx`, `688xxx`） | `cn` |
| 提及 港股/HKEX/恒生/港股通/北向/南向 | `hk` |
| 提及具体 港股代码（`00xxx.HK`） | `hk` |
| 提及 美股/纽交所/纳斯达克/NASDAQ/NYSE | `us` |
| 提及具体 美股 Ticker（`AAPL`, `TSLA`, `GOOGL`...） | `us` |
| 提及 BTC/ETH/加密货币/比特币/以太坊/SOL/合约/资金费率/爆仓/持仓量 | `crypto` |
| 提及 基金/ETF（A股） | `fund` |
| 提及 黄金/原油/铜/商品 | `commodity` |
| 提及 SPX/DJI/全球指数 | `index` |
| 仅提及技术指标名称，无市场 | 需追问市场 |

### Step 2：判断维度 → 路由到具体 skill

| 维度信号 | 路由目标 |
|----------|----------|
| 实时价格/现价/报价/行情 | `*-realtime-quote` |
| K线/日线/周线/月线/走势 | `*-kline` |
| 公司信息/PE/PB/换手率/股票列表 | `*-fundamental` |
| 涨跌停/停复牌/IPO/交易日历/复权 | `cn-market-mechanics` |
| 指数/上证/深证/申万/成分股 | `cn-index` |
| 港股通/北向资金/南向持仓 | `hk-connect` |
| 财报/利润表/资产负债表/现金流量表/分红 | `*-finance`（含在 market 聚合 skill 中） |
| RSI/MACD/KDJ/布林带/技术指标 | `cross-market-indicators`（股票）或 `crypto-indicators`（加密） |
| 资金费率/爆仓/持仓量/OI | `crypto-market` |
| 恐惧贪婪/AHR999/灰度/ETF流入 | `crypto-market` |

### Step 3：维度不明确 → 路由到市场聚合 skill

| 市场 | 聚合 skill |
|------|-----------|
| A股 | `cn-market` |
| 港股 | `hk-market` |
| 美股 | `us-market` |
| 加密货币 | `crypto-market` |

### 不支持的市场

| 市场 | 处理 |
|------|------|
| 期货/衍生品（除加密货币合约外） | 明确告知暂不支持 |
| 其他 | 明确告知暂不支持 |

---

## 执行策略

### 规则 1：多问题并行处理

```
用户问："分析腾讯，分析平安银行"

✅ 正确：
  ├── 并行：腾讯 K线 + 实时行情（hk-kline + hk-realtime-quote）
  ├── 并行：平安银行 K线 + 实时行情（cn-kline + cn-realtime-quote）
→ 汇总输出

❌ 错误：串行逐个完成
```

### 规则 2：数据采集并行，分析串行

数据采集阶段所有 API 调用并行 → 拿到数据后再做综合分析。

### 规则 3：失败快速放弃

- 404 → 检查端点，不换参数重试
- 最多尝试 2 个不同源，失败后用已有数据继续

### 规则 4：多指标用批量接口

- 股票技术指标：使用 `POST /api/v2/indicators/batch` 批量接口
- 加密货币技术指标：使用 `POST /api/v2/crypto/indicators/batch` 批量接口
- **禁止逐个调用单指标端点**

---

## 典型 Workflow

### 个股综合分析

```
cn-realtime-quote (实时行情)
  ∥ cn-kline (K线)
  ∥ cn-fundamental (每日指标 + 公司信息)
→ 综合输出
```

### 研报生成

```
cn-realtime-quote ∥ cn-kline ∥ cross-market-indicators → 聚类→分析→组装
```

### 多股对比（跨市场）

```
cn-realtime-quote (A股股)
  ∥ hk-realtime-quote (港股)
  ∥ us-realtime-quote (美股)
→ 对比输出
```

### 大盘概览

```
cn-index (所有指数并行)
  → 大盘总结
```

### 港股通资金分析

```
hk-connect (ggt-top10 + ggt-daily + hold 并行)
  → 资金流向分析
```

### BTC 综合分析

```
crypto-market (price + funding-rate + open-interest + liquidation + fear-greed)
  ∥ crypto-indicators (RSI + MACD + BOLL)
→ 综合输出
```

### 加密市场概览

```
crypto-market (fear-greed ∥ ahr999 ∥ btc-dominance ∥ etf-btc-flow ∥ ranking)
  → 市场概览总结
```

### 跨市场技术对比

```
cross-market-indicators (cn: 贵州茅台 RSI)
  ∥ crypto-indicators (binance: BTCUSDT RSI)
  ∥ cross-market-indicators (us: AAPL RSI)
→ 跨市场技术对比
```
