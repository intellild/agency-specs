## ADDED Requirements

### Requirement: Token storage
前端 SHALL 在登录成功后使用 jotai atomWithStorage 存储认证信息。

#### Scenario: Successful login callback
- **WHEN** 前端从 OAuth 回调中收到 JWT access token、refresh token 和 GitHub token
- **THEN** 前端使用 jotai atomWithStorage 将完整 Auth 对象存储到 localStorage 的 `auth` 键
- **AND** Auth 对象包含 accessToken、refreshToken、githubToken、userId、username
- **AND** 前端自动跳转到 dashboard 页面

### Requirement: Protected routes
前端 SHALL 保护需要认证的路由。

#### Scenario: Unauthenticated user accesses protected route
- **WHEN** 未登录用户（localStorage 无有效 JWT）访问 `/dashboard`
- **THEN** 前端自动重定向到 `/login` 页面

#### Scenario: Authenticated user accesses login page
- **WHEN** 已登录用户访问 `/login` 页面
- **THEN** 前端自动重定向到 `/dashboard` 页面

### Requirement: Include JWT in API requests
前端 SHALL 在向后端 API 发送请求时携带 JWT access token。

#### Scenario: Authenticated API call
- **WHEN** 前端向后端发送 API 请求
- **AND** 用户已登录（localStorage 中有 JWT）
- **THEN** 请求包含 `Authorization: Bearer <jwt>` header

#### Scenario: Access token expired during API call
- **WHEN** 后端返回 401 且提示 token 过期
- **THEN** 前端使用 refresh token 调用 `/api/auth/refresh` 获取新 token
- **AND** 使用新 access token 重试原请求
- **AND** 更新 jotai store 中的 auth 状态（自动同步到 localStorage）

### Requirement: Token refresh
前端 SHALL 自动刷新即将过期或已过期但仍可通过 refresh token 续期的 access token。

#### Scenario: Silent token refresh
- **WHEN** 前端发送 API 请求时
- **AND** access token 已过期或即将过期（剩余不足5分钟）
- **THEN** 前端自动调用 `/api/auth/refresh` 获取新 token
- **AND** 更新 jotai store 中的 auth 状态（自动同步到 localStorage）
- **AND** 使用新 access token 发送原请求

#### Scenario: Refresh token invalid
- **WHEN** 前端使用 refresh token 调用 `/api/auth/refresh`
- **AND** 后端返回 403 Forbidden
- **THEN** 前端清除 jotai store 中的 auth 状态（自动同步到 localStorage）
- **AND** 前端重定向到 `/login` 页面

### Requirement: Logout
前端 SHALL 支持用户退出登录。

#### Scenario: User logs out
- **WHEN** 用户点击退出登录按钮
- **THEN** 前端清除 jotai store 中的 auth 状态（自动同步到 localStorage）
- **AND** 前端重定向到 `/login` 页面
