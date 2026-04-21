# Error Troubleshooting

## 完整排错表

| 错误 | 原因 | 修复 |
|------|------|------|
| **404 端点不存在** | 调用了黑名单中的端点（如 `klines`、`profile`、`summary` 等） | 查 SKILL.md 的 Endpoint Blacklist，使用正确的端点 |
| 返回错误数据/空数据 | `symbol` vs `tsCode` 用反 | K线用 `symbol`，其他用 `tsCode` |
| 400 (行情端点) | 日期格式错 | 用 `YYYYMMDD` |
| 400 (指标端点) | 日期格式错 | 用 `YYYY-MM-DD` |
| 404 | 股票代码格式错 | A股: `600519.SH`; 港股: `00700.HK`; 美股: `AAPL` |
| 空数据 | 日期范围太窄或假期 | 先查交易日历，扩大范围 |
| 400 missing market | 指标缺 `market` | 必填: `cn`/`hk`/`us` |
| 指标全 null | warmup 数据不足 | 增大 `limit` |
| BOLL 结果错误 | V1 习惯用 `nbdev_up`/`nbdev_dn` | V2 用单个 `nbdev`（默认 2.0） |
| securities 返回空数据 | codes 带了交易所后缀 | A股用纯数字 `600519`，不要用 `600519.SH`；港股用 `00700`，不要用 `00700.HK` |
| 港股无最新数据 | `limit` 参数 bug | 改用 `startDate` + `endDate` 参数 |

## Error Codes

| Code | 含义 |
|------|------|
| 400 | 参数错误 |
| 401 | API key 无效 |
| 404 | 标的不存在或端点不存在 |
| 500 | 内部错误 |

## 常见排查顺序

1. 检查端点是否在黑名单中
2. 检查 `symbol` vs `tsCode` vs `codes` 是否用对
3. 检查股票代码是否带了正确的交易所后缀
4. 检查日期格式（行情 `YYYYMMDD`，指标 `YYYY-MM-DD`）
5. 检查 `market` 参数是否填写（指标端点必填）
6. 检查 securities 是否误带了交易所后缀
