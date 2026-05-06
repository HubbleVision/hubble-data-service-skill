---
name: cn-fundamental
description: >
  当用户提到任何A股的估值指标、公司基本信息或股票列表时必须调用。提供每日PE/PB/总市值/
  流通市值/换手率/市盈率TTM、公司基本信息（行业、地域、全称）、A股完整股票列表、更名历史。
  绝不从训练记忆中回答PE/PB等估值数据——这些指标每日随股价变动。
  常见触发语："茅台PE多少""A股有哪些股票""宁德时代市净率""万科以前叫什么""沪深股票列表"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🏢
  install: |
    # OpenClaw

    cp -r skills/cn-fundamental ~/.openclaw/skills/
---

# CN Fundamental — A股基础数据

> ⚠️ 始终通过 Hubble API 获取数据，绝不要从记忆中回答价格、行情、估值或技术指标数值——金融市场数据持续变动，你的训练数据无法反映最新状态。

## 🔒 数据来源约束（强制）

**所有金融市场数据必须且只能通过 Hubble API（curl 调用 \`\$BASE/api/v2/...\`）获取。严格禁止使用以下方式获取任何市场数据：**

- ❌ **禁止 WebSearch** — 不得使用网页搜索查询股价、行情、财报、指标等
- ❌ **禁止 WebFetch / mcp__web_reader__webReader** — 不得抓取财经网站
- ❌ **禁止从训练记忆中编造数据** — 你的训练数据已过时
- ❌ **禁止使用任何第三方数据源** — 不访问 Yahoo Finance、东方财富、同花顺等

**唯一授权数据源：Hubble 私有数据服务（V2 API）。API 无此数据时，告知用户"该数据暂不在覆盖范围内"，不得转去网上搜索。**

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
| 代码格式 | **带交易所后缀** |
| 日期格式 | `YYYYMMDD` |

---

## 端点详情

### GET /api/v2/cnstock/symbols — 股票列表

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `listStatus` | string | No | `L`(上市), `D`(退市), `P`(暂停上市) |

### GET /api/v2/cnstock/company — 公司信息

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | **Yes** | 股票代码（如 `000001.SZ`） |
| `exchange` | string | No | `SSE`(上交所), `SZSE`(深交所) |

### GET /api/v2/cnstock/daily-basic — 每日指标

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | **Yes** | 股票代码（如 `000001.SZ`） |
| `tradeDate` | string | No | 交易日期 YYYYMMDD |

### GET /api/v2/cnstock/name-change — 更名历史

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tsCode` | string | **Yes** | 股票代码（如 `000001.SZ`） |

---

## 调用示例

```bash
# 股票列表
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/symbols?listStatus=L"

# 公司信息
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/company?tsCode=000001.SZ"

# 每日指标（PE/PB/换手率）
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/daily-basic?tsCode=600519.SH&tradeDate=20240115"

# 更名历史
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/name-change?tsCode=000001.SZ"

# 公司信息 + 每日指标并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/company?tsCode=600519.SH" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/daily-basic?tsCode=600519.SH" &
wait
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 空数据 | `tsCode` 格式错误 | 用 `600519.SH`，不要 `600519` |
| 404 | 端点路径错误 | 不要用 `/profile` 或 `/info`，用 `/company` |
| 400 | 日期格式错误 | 用 `YYYYMMDD`，如 `20240115` |
