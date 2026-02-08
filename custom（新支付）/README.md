# Custom 支付模式 (新)

## 概述

使用 Stripe Elements 嵌入式支付，响应返回 `client_secret` 用于 Stripe.js。

**支持两种支付方式：**
1. **嵌入式支付** - 使用 Stripe.js 在页面内完成支付
2. **跳转支付** - 构造 URL 跳转到 ChatGPT 内嵌支付页面 ⭐ 推荐

## 支付流程

```
1. POST /backend-api/payments/checkout → 创建 checkout session
              ↓
2. 获取 processor_entity 和 checkout_session_id
              ↓
3. 构造跳转 URL 或使用 Stripe.js
              ↓
4. 完成支付 → 订阅激活
```

## 跳转 URL 构造

虽然响应中 `url` 为 null，但可以通过以下方式构造跳转 URL：

```javascript
const url = `https://chatgpt.com/checkout/${processor_entity}/${checkout_session_id}`;
// 例如: https://chatgpt.com/checkout/openai_llc/cs_live_xxx
```

> [!TIP]
> **推荐使用跳转方式**
> 
> 跳转到 `chatgpt.com/checkout/...` 页面后，会显示 ChatGPT 官方的嵌入式支付 UI，无需自己实现 Stripe Elements。

## 特点

| 字段 | 值 |
|------|-----|
| `checkout_ui_mode` | `custom` |
| `tag` | `custom_checkout_session` |
| `processor_entity` | `openai_llc` |
| `client_secret` | 有值 (用于 Stripe.js) |
| `url` | null (需构造) |

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
