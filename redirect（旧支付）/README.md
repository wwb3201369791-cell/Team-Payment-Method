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
- `processor_entity`: `openai_ie`
- 响应包含 `url` 字段用于跳转
- `client_secret` 为 null

## 国家切换
通过修改请求中的 `billing_details` 字段可切换到任意国家：
```json
"billing_details": {
    "country": "{country_code}",  // ISO 3166-1 alpha-2 国家代码
    "currency": "{currency_code}" // ISO 4217 货币代码
}
```

## 获取 Access Token
在 ChatGPT 页面控制台执行以下代码获取 Token：
```javascript
// 方法1：直接获取并打印
fetch('https://chatgpt.com/api/auth/session').then(r => r.json()).then(d => console.log(d.accessToken));

// 方法2：在脚本中使用
const session = await fetch('https://chatgpt.com/api/auth/session').then(r => r.json());
const token = session.accessToken;
```
