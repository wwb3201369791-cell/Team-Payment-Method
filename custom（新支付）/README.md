# Custom 支付模式 (新)

## 概述
使用 Stripe Elements 嵌入式支付，用户在 ChatGPT 页面内直接完成付款，无需跳转。

## 支付流程
```
1. POST /backend-api/sentinel/req     → 获取 sentinel token
              ↓
2. POST /backend-api/payments/checkout → 创建 checkout session
              ↓
3. 使用 Stripe.js + client_secret    → 页面内嵌入式支付UI
              ↓
4. 用户在当前页面完成支付            → 直接激活订阅
```

## 特点
- `checkout_ui_mode`: `custom`
- `tag`: `custom_checkout_session`
- `processor_entity`: `openai_llc` (美国实体)
- 响应包含 `client_secret` 用于 Stripe.js
- `url` 为 null (无跳转)

## 适用地区
主要用于美国用户 (USD 货币)
