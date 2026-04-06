## 1. Backend Dependencies & Setup

- [x] 1.1 Install JWT library (`jose`)
- [x] 1.2 Install GitHub OAuth 相关库 (`octokit`)
- [x] 1.3 生成 RSA 密钥对（私钥用于签名 access token，公钥用于验证）
- [x] 1.4 配置环境变量：`GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`, `ROOT_SECRET`

## 2. Backend OAuth Implementation

- [x] 2.1 创建 `/auth/github/callback` POST 端点，处理授权码交换
- [x] 2.2 实现 GitHub token 交换逻辑（用 code 换 access_token）
- [x] 2.3 实现获取 GitHub 用户信息逻辑
- [x] 2.4 实现 JWT access token 生成（RS256 签名，30天有效期），payload 包含 GitHub 用户 ID 和 type="access"
- [x] 2.5 实现 refresh token 生成（根密钥对称加密 HS256），payload 包含 user ID 和 type="refresh"

## 3. Backend Token Refresh

- [x] 3.1 创建 `/auth/refresh` 端点，接收 refresh token
- [x] 3.2 实现 refresh token 验证逻辑（使用根密钥解密/验证）
- [x] 3.3 验证 refresh token 的 type 字段为 "refresh"
- [x] 3.4 实现新的 JWT access token 生成（RS256 签名，30天有效期）
- [x] 3.5 实现新的 refresh token 生成（实现 token 轮换机制）
- [x] 3.6 无需创建 `/auth/logout` 端点（无状态认证，前端直接清除 token）

## 4. Backend JWT Authentication

- [x] 4.1 注册 JWT 验证插件，配置公钥用于验证 access token
- [x] 4.2 创建 JWT 验证装饰器/中间件，验证 type="access"
- [x] 4.3 保护需要认证的 API 端点

## 5. Frontend Login Page

- [x] 5.1 创建 `/login` 路由和页面组件
- [x] 5.2 实现服务器地址输入框，默认值 `http://localhost:3000`
- [x] 5.3 实现 GitHub Client ID 输入框
- [x] 5.4 实现"使用 GitHub 登录"按钮
- [x] 5.5 实现点击登录生成 state 并跳转到 GitHub OAuth 授权页面

## 6. Frontend OAuth Callback Handling

- [x] 6.1 创建 `/oauth/callback` 路由和页面组件
- [x] 6.2 从 URL 参数中获取 code 和 state
- [x] 6.3 验证 state 参数防止 CSRF
- [x] 6.4 发送 POST 请求到后端 `/auth/github/callback` 交换 token
- [x] 6.5 使用 jotai atomWithStorage 将 Auth 对象存储到 localStorage
- [x] 6.6 登录成功后自动跳转到 dashboard

## 7. Frontend Route Guard

- [x] 7.1 实现检查 localStorage 中 JWT 的函数
- [x] 7.2 创建受保护路由的高阶组件/守卫
- [x] 7.3 配置 dashboard 路由需要认证
- [x] 7.4 实现未登录用户自动跳转到登录页
- [x] 7.5 实现已登录用户访问登录页自动跳转到 dashboard

## 8. Frontend Token Refresh

- [x] 8.1 创建 token 刷新服务，封装 `/api/auth/refresh` 调用
- [x] 8.2 应用启动时检查 token 过期时间，自动刷新即将过期的 token
- [x] 8.3 拦截 401 响应，使用 refresh token 自动刷新并重试请求
- [x] 8.4 刷新成功后更新 localStorage 中的 token
- [x] 8.5 refresh token 失效时（403）清除存储并跳转到登录页

## 9. Frontend API Integration

- [x] 9.1 修改 API 客户端，自动添加 `Authorization: Bearer <jwt>` header
- [x] 9.2 处理 401/403 响应，触发刷新或登出流程

## 10. Dashboard Integration

- [x] 10.1 确认或创建 dashboard 页面组件
- [x] 10.2 添加退出登录按钮，清除 jotai auth 状态并跳转到登录页
- [x] 10.3 可选：在 dashboard 显示 GitHub 用户信息

## 11. Testing & Verification

- [x] 11.1 验证完整的登录流程：登录页 → GitHub → 回调 → Dashboard
- [x] 11.2 验证未登录用户无法访问 dashboard（AuthGuard 重定向）
- [x] 11.3 验证已登录用户访问登录页自动跳转到 dashboard
- [x] 11.4 验证退出登录功能（前端清除 jotai auth 状态）
- [x] 11.5 验证 JWT 验证保护后端 API（RS256 公钥验证）
- [x] 11.6 验证 token 刷新流程（access token 即将过期时自动刷新）
- [x] 11.7 验证 API 请求自动携带 Authorization header
- [x] 11.8 验证使用错误私钥签名的 token 无法通过验证
