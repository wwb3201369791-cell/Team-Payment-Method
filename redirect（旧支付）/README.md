# Redirect 支付模式 (旧)

## 概述

使用 Stripe Hosted Checkout 页面，响应直接返回支付 URL，用户跳转到 Stripe 托管的支付页面完成付款。

## 支付流程

```
1. POST /backend-api/payments/checkout → 创建 checkout session
              ↓
2. 获取响应中的 url 字段
              ↓
3. 跳转到 Stripe 托管支付页面
              ↓
4. 完成支付 → 自动跳回 ChatGPT → 订阅激活
```

## 跳转 URL

响应直接返回 `url` 字段，可直接跳转：

```javascript
// 响应中的 url 字段
const url = data.url;
// 例如: https://pay.openai.com/c/pay/cs_live_xxx

window.location.href = url;
```

## 特点

| 字段 | 值 |
|------|-----|
| `checkout_ui_mode` | `redirect` |
| `tag` | `hosted_checkout_session` |
| `processor_entity` | `openai_ie` |
| `url` | 有值 (直接跳转) |
| `client_secret` | null |

## 与 Custom 模式对比

| 特性 | Redirect (旧) | Custom (新) |
|------|--------------|-------------|
| **响应 URL** | 直接返回 | 需构造 |
| **支付页面** | Stripe 托管 | ChatGPT 内嵌 |
| **URL 格式** | `pay.openai.com/c/pay/{id}` | `chatgpt.com/checkout/{entity}/{id}` |
| **处理实体** | `openai_ie` (爱尔兰) | `openai_llc` (美国) |

## 国家切换

通过修改请求中的 `billing_details` 字段可切换到任意国家：

```json
"billing_details": {
    "country": "KR",   // ISO 3166-1 alpha-2 国家代码
    "currency": "KRW"  // ISO 4217 货币代码 (必须大写)
}
```

## 获取 Access Token

```javascript
fetch('https://chatgpt.com/api/auth/session')
  .then(r => r.json())
  .then(d => console.log(d.accessToken));
```
