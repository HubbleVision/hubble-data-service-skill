---
name: cn-realtime-quote
description: >
  只要用户提到任何A股股票代码（600519, 000001, 300750...）或股票名称（茅台, 平安银行, 宁德时代...）
  并询问现价、涨跌幅、成交量、成交额、市值、换手率、市盈率、市净率、盘口买卖五档，
  必须立即调用此 skill——绝不从训练记忆中回答，A股实时行情持续变动，你的训练数据无法反映最新状态。
  常见触发语："茅台现在多少钱""大盘怎么样""宁德时代涨了没""今天涨停的股票""沪深两市成交额"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 📊
  install: |
    # OpenClaw

    cp -r skills/cn-realtime-quote ~/.openclaw/skills/
---

# CN Realtime Quote — A股实时行情

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
| 参数名 | `codes` |
| 代码格式 | **纯数字，不带交易所后缀** |
| 单次上限 | 500 个代码 |
| 分隔符 | 英文逗号 |

| 市场 | 示例 |
|------|------|
| 上交所 (SH) | `600519` |
| 深交所 (SZ) | `000001` |
| 北交所 (BJ) | `430047` |

**❌ 错误写法**：`600519.SH`、`SH600519`、`sh600519`

---

## 端点详情

### GET /api/v2/cnstock/securities

批量查询 A股 实时报价。

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | **Yes** | 证券代码，逗号分隔（如 `600519,000001,300750`） |
| `fields` | string | No | 返回字段，逗号分隔（不传返回全部） |

---

## 调用示例

```bash
# 查询单只股票
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519"

# 批量查询
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519,000001,300750"

# 只查指定字段
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519&fields=price,chgPct,volume"

# 多股票并行查询
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=600519" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/cnstock/securities?codes=000001" &
wait
```

**响应示例**：

```json
{
  "success": true,
  "data": {
    "600519": {
      "name": "贵州茅台",
      "price": 1688.50,
      "chgPct": 1.25,
      "volume": 3500000,
      "high": 1695.00,
      "low": 1675.00,
      "open": 1680.00
    }
  },
  "timestamp": 1710865200000
}
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 返回空数据 | `codes` 带了后缀 | 用纯数字：`600519`，不要 `600519.SH` |
| 400 | API key 无效 | 联系管理员检查 API Key |
| 部分代码无数据 | 代码不存在或已退市 | 核对代码是否正确 |
