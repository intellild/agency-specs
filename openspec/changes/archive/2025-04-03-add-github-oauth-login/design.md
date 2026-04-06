## Context

当前系统是一个基于 Nx monorepo 的 Web 应用，包含 Fastify 后端（agency）和 Modern.js + React 前端（client）。系统目前没有用户认证机制，所有用户都可以直接访问所有页面。本设计引入 GitHub OAuth 认证流程，使用户能够通过 GitHub 账号登录，并为后续访问 GitHub API 提供认证令牌。

## Goals / Non-Goals

**Goals:**
- 实现完整的 GitHub OAuth 登录流程
- 使用 JWT access token 进行有状态的会话管理（30天有效期，RS256 签名）
- 使用 refresh token 实现自动续期（长期有效，根密钥 HS256 对称加密）
- 前端登录页支持服务器地址和 GitHub Client ID 配置
- 前端使用 jotai atomWithStorage 存储和携带认证信息
- 保护 dashboard 等敏感路由，仅允许已登录用户访问
- 前端直接构建 GitHub OAuth URL，后端只处理 token 交换

**Non-Goals:**
- 不实现用户注册功能（GitHub OAuth 即注册）
- 不实现复杂的用户权限管理（仅区分登录/未登录）
- 不在后端存储 token（纯无状态认证）
- 不实现服务端会话管理
- 不实现后端 `/auth/github/login` 端点（前端直接跳转）
- 不实现后端 `/auth/logout` 端点（前端直接清除状态）

## Decisions

### 1. 前端直接构建 GitHub OAuth URL
**选择**: 前端直接构建 GitHub OAuth 授权 URL，而非后端提供登录端点
**理由**: 
- 简化后端架构，不需要 `/auth/github/login` 端点
- 前端需要收集 GitHub Client ID，直接构建 URL 更直观
- state 参数生成和验证在前端完成，减少一次后端往返

### 2. Access Token 使用非对称加密（RS256）
**选择**: 使用 RSA 密钥对，私钥签名 access token，公钥验证
**理由**:
- 私钥仅在服务器端使用，安全性更高
- 公钥可以公开分发，用于验证 token 而不需要暴露私钥
- 符合 JWT 最佳实践，支持分布式验证（如未来需要多个服务验证 token）
- 部署时只需要配置私钥，公钥可以从私钥派生或单独配置

### 3. Refresh Token 使用对称加密（根密钥）
**选择**: 使用根密钥（root secret）对 refresh token 进行对称加密
**理由**:
- refresh token 只用于后端刷新流程，不需要外部验证
- 对称加密性能更好，实现更简单
- 根密钥不暴露给客户端，安全性有保障
- 通过 token 轮换机制，旧的 refresh token 自然失效

### 4. Token 无后端存储
**选择**: 两个 token 都不在后端存储，后端只通过密钥验证有效性
**理由**:
- 纯无状态设计，简化后端架构，无需数据库/缓存
- 后端重启或扩容不影响已有 token
- 退出登录由前端删除 token 即可，无需后端 `/auth/logout` 端点
- Token 自带完整性保护（签名），无法伪造

### 5. 使用 jotai atomWithStorage 管理认证状态
**选择**: 使用 jotai atomWithStorage 存储 Auth 对象到 localStorage
**理由**:
- 与 React 状态管理集成，自动同步 localStorage 和组件状态
- Auth 对象包含 accessToken、refreshToken、githubToken、userId、username
- localStorage 键名为 `auth`，使用 jotai 存储机制
- 跨页面刷新保持登录状态

### 6. 登录流程采用前后端分离模式
**选择**: 前端处理 OAuth callback 页面，通过 POST API 与后端交换 token
**理由**:
- 前端控制整个用户流程，更好的 UX 控制（加载状态、错误处理）
- 后端提供 `/auth/github/callback` POST API 返回 JSON，不处理重定向
- state 参数在前端生成和验证，后端不需要处理 state
- 前端使用 `ofetch` 发送 code 到后端，接收 token 后存储并跳转

## Risks / Trade-offs

| 风险 | 缓解措施 |
|------|----------|
| Token 存储在 localStorage（通过 jotai），存在 XSS 风险 | 使用 CSP 策略，谨慎处理第三方脚本；后续迭代考虑 httpOnly cookie |
| Refresh token 长期有效，泄露风险 | Refresh token 只能用于换取 access token；实现 token 轮换（刷新时生成新 token）；泄露后只能等待下次刷新 |
| GitHub OAuth token 泄露风险 | token 仅存储在客户端，不经过后端存储；建议用户定期撤销授权 |
| 单点故障依赖 GitHub | 考虑后续添加其他 OAuth 提供商作为备选 |
| 私钥泄露风险 | 私钥存储在文件系统，不在代码中硬编码；通过 `JWT_PRIVATE_KEY` 环境变量或 `keys/<env>/private.pem` 文件加载 |
| state 验证在前端，可能被绕过 | state 存储在 sessionStorage，同源策略提供基础保护；后续可考虑后端验证增强安全 |

## Migration Plan

1. **开发阶段**: 配置测试用的 GitHub OAuth App（回调地址指向本地开发环境 `http://localhost:8080/oauth/callback`）
2. **部署配置**: 为生产环境创建独立的 GitHub OAuth App
3. **密钥生成**: 生成 RSA 密钥对（私钥用于签名 access token，公钥用于验证）
   - 密钥存储在 `apps/server/src/app/routes/keys/<env>/` 目录
4. **环境变量配置**:
   - `GITHUB_CLIENT_ID` - GitHub OAuth App Client ID（前端可配置，环境变量为默认值）
   - `GITHUB_CLIENT_SECRET` - GitHub OAuth App Client Secret
   - `ROOT_SECRET` - 根密钥，用于加密/验证 refresh token（HS256）
   - `FRONTEND_URL` - 前端地址，用于 CORS 和回调（默认 `http://localhost:8080`）
5. **无需数据迁移**: 新功能，无现有数据影响

## Open Questions

1. 是否需要后端存储用户信息以便后续功能扩展？
2. GitHub token 过期后是否需要自动刷新？（目前 GitHub OAuth token 长期有效）
3. 是否需要支持多个 GitHub OAuth App 配置（多环境部署）？
4. 是否需要 refresh token 绑定设备/IP 等额外安全机制？
