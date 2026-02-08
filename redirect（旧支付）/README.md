# Redirect 支付模式 (旧)

## 概述
使用 Stripe Hosted Checkout 页面，用户会被跳转到 Stripe 托管的支付页面完成付款。

## 支付流程
```
1. POST /backend-api/sentinel/req     → 获取 sentinel token
              ↓
2. POST /backend-api/payments/checkout → 创建 checkout session
              ↓
3. 跳转到响应中的 url 字段           → Stripe 托管支付页面
              ↓
4. 用户在 Stripe 页面完成支付        → 自动跳回 ChatGPT
```

## 特点
- `checkout_ui_mode`: `redirect`
- `tag`: `hosted_checkout_session`
- `processor_entity`: `openai_ie` (爱尔兰实体)
- 响应包含 `url` 字段用于跳转
- `client_secret` 为 null

## 适用地区
主要用于欧洲用户 (EUR 货币)
