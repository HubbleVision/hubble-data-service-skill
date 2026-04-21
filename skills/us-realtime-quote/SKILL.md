---
name: us-realtime-quote
description: >
  美股实时行情查询。批量获取美股证券实时报价。
  当用户询问"苹果实时价格""美股实时行情""AAPL报价""美股现价"时触发。
user-invocable: true
metadata:
  openclaw: 1.0.0
  emoji: 💵
  install: |
    # OpenClaw

    cp -r skills/us-realtime-quote ~/.openclaw/skills/
---

# US Realtime Quote — 美股实时行情

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
| 代码格式 | **Ticker 符号** |
| 单次上限 | 500 个代码 |
| 分隔符 | 英文逗号 |

| 示例 | 说明 |
|------|------|
| `AAPL` | Apple Inc. |
| `TSLA` | Tesla |
| `MSFT` | Microsoft |

美股使用标准 Ticker 符号，无后缀混淆风险。

---

## 端点详情

### GET /api/v2/usstock/securities

批量查询美股实时报价。价格字段已按 power 动态换算，百分比字段已除以 100。

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `codes` | string | **Yes** | Ticker 符号，逗号分隔（如 `AAPL,TSLA,MSFT`） |
| `fields` | string | No | 指定返回字段，逗号分隔（不传返回全部） |

---

## 调用示例

```bash
# 查询单只股票
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL"

# 批量查询
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL,TSLA,MSFT"

# 只查指定字段
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL&fields=price,chgPct,volume"

# 多股票并行
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=AAPL" &
curl -sS "${AUTH[@]}" "$BASE/api/v2/usstock/securities?codes=TSLA" &
wait
```

**响应示例**：

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

## 错误排查

| 错误 | 原因 | 修复 |
|------|------|------|
| 返回空数据 | Ticker 符号错误 | 检查大小写和拼写 |
| 400 | API key 无效 | 联系管理员检查 API Key |
