# ChatGPT Team Payment Method

本仓库记录了 ChatGPT Team 订阅的两种支付模式及其 API 请求详情。

## 支付模式对比

| 特性 | Custom (新) | Redirect (旧) |
|------|-------------|---------------|
| **UI 模式** | `custom` | `redirect` |
| **Tag** | `custom_checkout_session` | `hosted_checkout_session` |
| **处理实体** | `openai_llc` | `openai_ie` |
| **支付页面** | 嵌入式 (Stripe Elements) | 跳转到 Stripe 托管页面 |
| **响应特征** | 返回 `client_secret` | 返回 `url` |

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
3. 完成支付 (方式因模式而异)
              ↓
4. 订阅激活
```

## 国家/货币切换

两种模式都支持通过 `billing_details` 字段切换国家和货币：

```json
"billing_details": {
    "country": "{country_code}",  // ISO 3166-1 alpha-2 国家代码 (如: US, JP, GB)
    "currency": "{currency_code}" // ISO 4217 货币代码 (如: USD, JPY, GBP)
}
```

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
