---
name: hk-realtime-quote
description: >
  只要用户提到任何港股代码（00700, 09988, 09888, 01810...）或港交所上市公司的现价、涨跌幅、
  成交量、市值、52周高低，必须立即调用此 skill——绝不从训练记忆中回答，港股实时行情持续变动。
  常见触发语："腾讯现在多少""美团报价""小米涨了没""港股恒生成分股实时行情""港交所现价"。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 🏛️
  install: |
    # OpenClaw

    cp -r skills/hk-realtime-quote ~/.openclaw/skills/
---

# HK Realtime Quote — 港股实时行情

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
| 代码格式 | **纯数字，不带 `.HK` 后缀** |
| 单次上限 | 500 个代码 |
| 分隔符 | 英文逗号 |

| 示例 | 说明 |
|------|------|
| `00700` | 腾讯控股 |
| `09988` | 阿里巴巴 |
| `09888` | 百度 |

**❌ 错误写法**：`00700.HK`、`HK00700`

---

## 端点详情

### GET /api/v2/hkstock/securities

批量查询港股实时报价。

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | **Yes** | 证券代码，逗号分隔（如 `00700,09988,09888`） |
| `fields` | string | No | 返回字段，逗号分隔（不传返回全部） |

---

## 调用示例

```bash
# 查询单只股票
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700"

# 批量查询
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700,09988,09888"

# 只查指定字段
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700&fields=price,chgPct,volume"

# 多股票并行查询
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=00700" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/hkstock/securities?codes=09988" &
wait
```

**响应示例**：

```json
{
  "success": true,
  "data": {
    "00700": {
      "name": "腾讯控股",
      "price": 375.20,
      "chgPct": -0.53,
      "volume": 12000000
    }
  },
  "timestamp": 1710865200000
}
```

---

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 返回空数据 | `codes` 带了 `.HK` 后缀 | 用纯数字：`00700`，不要 `00700.HK` |
| 400 | API key 无效 | 联系管理员检查 API Key |
| 部分代码无数据 | 代码不存在 | 核对港股代码是否正确 |
