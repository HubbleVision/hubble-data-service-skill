# 其他金融市场 API Reference

> 基金、商品、全球指数的 API 参考文档。

---

## 基金

> **通用参数**：`startDate`, `endDate`, `limit`(1-5000, 默认100)。

| Endpoint | 代码参数 | 额外参数 | 说明 |
|----------|----------|----------|------|
| `/api/v2/fund/basic` | — | `listStatus`: L/D/P | 基金列表 |
| `/api/v2/fund/nav` | `tsCode` | — | 基金净值 |
| `/api/v2/fund/daily` | `tsCode` | `startDate`, `endDate`, `limit` | ETF 日线行情 |
| `/api/v2/fund/portfolio` | `tsCode` | — | 基金持仓 |
| `/api/v2/fund/etf-basic` | — | `listStatus`: L/D/P | ETF 列表 |
| `/api/v2/fund/share` | `tsCode` | — | 基金份额 |
| `/api/v2/fund/div` | `tsCode` | — | 基金分红 |
| `/api/v2/fund/manager` | `tsCode` | — | 基金经理 |
| `/api/v2/fund/company` | `tsCode` | — | 基金公司 |

```bash
# ETF 列表
curl -sS "${AUTH[@]}" "$BASE/api/v2/fund/etf-basic?listStatus=L"

# ETF 日线
curl -sS "${AUTH[@]}" "$BASE/api/v2/fund/daily?tsCode=159915.SZ&limit=100"

# 基金净值
curl -sS "${AUTH[@]}" "$BASE/api/v2/fund/nav?tsCode=159915.SZ"

# 基金持仓
curl -sS "${AUTH[@]}" "$BASE/api/v2/fund/portfolio?tsCode=159915.SZ"
```

---

## 大宗商品

### 贵金属现货

`GET /api/v2/commodity/spot`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | No | 品种代码：`GOLD`, `SILVER`, `PLATINUM`, `PALLADIUM` |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/commodity/spot?symbol=GOLD"
```

### 商品 K 线

`GET /api/v2/commodity/klines`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | No | 品种代码：`GOLD`, `SILVER`, `PLATINUM`, `PALLADIUM` |
| `interval` | string | No | 周期：`daily`, `weekly`, `monthly` |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/commodity/klines?symbol=GOLD&interval=daily"
```

### 大宗商品价格

`GET /api/v2/commodity/{name}`

| Path Parameter | 说明 |
|----------------|------|
| `WTI` | WTI 原油 |
| `BRENT` | 布伦特原油 |
| `NATURAL_GAS` | 天然气 |
| `COPPER` | 铜 |
| `ALUMINUM` | 铝 |

| Query Parameter | Type | Description |
|-----------------|------|-------------|
| `interval` | string | 周期：`daily`, `weekly`, `monthly` |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/commodity/WTI?interval=daily"
curl -sS "${AUTH[@]}" "$BASE/api/v2/commodity/BRENT"
curl -sS "${AUTH[@]}" "$BASE/api/v2/commodity/COPPER?interval=weekly"
```

---

## 全球指数

### 指数目录

`GET /api/v2/index/symbols`

获取全球指数目录（无参数）。

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/index/symbols"
```

### 指数 K 线

`GET /api/v2/index/klines`

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | **Yes** | 指数代码 |
| `interval` | string | No | 周期：`daily`, `weekly`, `monthly` |
| `limit` | int | No | 1-500 (默认 100) |

```bash
curl -sS "${AUTH[@]}" "$BASE/api/v2/index/klines?symbol=SPX&interval=daily&limit=100"
curl -sS "${AUTH[@]}" "$BASE/api/v2/index/klines?symbol=DJI&interval=weekly"
```
