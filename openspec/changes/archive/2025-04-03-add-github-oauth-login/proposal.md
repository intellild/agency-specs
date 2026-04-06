## Why

用户需要通过 GitHub OAuth 进行身份验证，以便安全地访问系统。当前的系统缺乏用户认证机制，无法识别和授权用户访问受保护的资源（如 dashboard）。集成 GitHub OAuth 可以利用用户已有的 GitHub 账号，简化登录流程，同时获取 GitHub API 访问令牌以便后续与 GitHub 服务交互。

## What Changes

- 新增 GitHub OAuth 登录流程，支持用户通过 GitHub 账号认证
- 后端新增 OAuth 回调接口，处理 GitHub 授权码交换和用户信息获取
- 后端实现 JWT access token 签发（RS256 非对称签名，30天有效期）
- 后端实现 refresh token 签发（根密钥对称加密，长期有效）
- 前端新增登录页面，支持服务器地址配置（默认 http://localhost:3000）
- 前端实现 OAuth 登录跳转和回调处理
- 前端存储 JWT 和 GitHub token 到 localStorage
- 前端增加路由守卫，未登录用户只能访问登录页，登录后自动跳转 dashboard

## Capabilities

### New Capabilities
- `github-oauth`: GitHub OAuth 登录认证流程，包括授权跳转、回调处理、Token 交换
- `jwt-auth`: JWT 认证机制，基于 GitHub 用户 ID 生成和验证令牌
- `auth-guard`: 前端路由守卫，保护需要认证的页面

### Modified Capabilities
<!-- 无现有规范需要修改 -->

## Impact

- **后端 API**: 新增 `/auth/github/login` 和 `/auth/github/callback` 端点
- **前端路由**: 新增 `/login` 路由，修改默认路由逻辑
- **依赖**: 后端需要添加 JWT 库和 GitHub OAuth 相关库
- **配置**: 需要 GitHub OAuth App 的 Client ID 和 Client Secret
- **存储**: 客户端 localStorage 将存储 `jwt` 和 `github_token`
