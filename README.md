# ChatGPT Team Payment Method

本仓库记录了 ChatGPT Team 订阅的两种支付模式及其 API 请求详情。

## 支付模式对比

| 特性 | Custom (新) | Redirect (旧) |
|------|-------------|---------------|
| **UI 模式** | `custom` | `redirect` |
| **Tag** | `custom_checkout_session` | `hosted_checkout_session` |
| **处理实体** | `openai_llc` | `openai_ie` |
| **支付页面** | ChatGPT 内嵌页面 | Stripe 托管页面 |
| **响应特征** | 返回 `client_secret` | 返回 `url` |
| **跳转方式** | 构造 URL 跳转 | 直接使用返回的 URL |

## 跳转 URL 格式

两种模式都可以跳转到支付页面，但 URL 格式不同：

| 模式 | 跳转 URL 格式 |
|------|--------------|
| **Custom** | `https://chatgpt.com/checkout/{processor_entity}/{session_id}` |
| **Redirect** | 直接使用响应中的 `url` 字段 (如: `https://pay.openai.com/c/pay/{session_id}`) |

> [!TIP]
> **Custom 模式也支持跳转！**
> 
> 虽然 Custom 模式响应的 `url` 为 null，但可以通过 `processor_entity` 和 `checkout_session_id` 构造跳转 URL。

## 目录结构

```
├── custom（新支付）/          # 嵌入式支付模式
│   ├── 01_sentinel_request.txt   # Sentinel Token 请求
│   ├── 02_checkout_request.txt   # Checkout 请求
│   └── README.md                 # 详细说明
│
└── redirect（旧支付）/        # 跳转支付模式
    ├── 01_sentinel_request.txt   # Sentinel Token 请求
    ├── 02_checkout_request.txt   # Checkout 请求
    └── README.md                 # 详细说明
```

## 通用流程

两种模式都遵循相同的基本流程：

```
1. POST /backend-api/sentinel/req     → 获取 sentinel token
              ↓
2. POST /backend-api/payments/checkout → 创建 checkout session
              ↓
3. 跳转到支付页面 (URL 格式因模式而异)
              ↓
4. 完成支付 → 订阅激活
```

## 国家/货币切换

两种模式都支持通过 `billing_details` 字段切换国家和货币：

```json
"billing_details": {
    "country": "{country_code}",  // ISO 3166-1 alpha-2 国家代码 (如: US, JP, KR)
    "currency": "{currency_code}" // ISO 4217 货币代码 (如: USD, JPY, KRW)
}
```

> [!IMPORTANT]
> **货币代码必须使用大写字母**
> 
> 货币代码（如 `USD`, `JPY`, `KRW`）必须使用大写形式。使用小写（如 `usd`）会导致请求失败。

## 获取 Access Token

在 ChatGPT 页面控制台执行：

```javascript
fetch('https://chatgpt.com/api/auth/session')
  .then(r => r.json())
  .then(d => console.log(d.accessToken));
```

## 详细文档

- [Custom 支付模式 (新)](./custom（新支付）/README.md)
- [Redirect 支付模式 (旧)](./redirect（旧支付）/README.md)
