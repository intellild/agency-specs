## ADDED Requirements

### Requirement: JWT access token generation
后端 SHALL 使用非对称加密（RS256）签发 JWT access token。

#### Scenario: Successful login
- **WHEN** 用户成功通过 GitHub OAuth 认证
- **THEN** 后端生成 JWT access token，payload 包含：
  - `sub`: GitHub 用户 ID（字符串）
  - `iat`: 签发时间戳
  - `exp`: 过期时间戳（30天后）
  - `type`: `"access"`
- **AND** JWT 使用私钥进行 RS256 签名
- **AND** 公钥用于后续验证

### Requirement: Refresh token generation
后端 SHALL 使用对称加密签发 refresh token。

#### Scenario: Login or refresh
- **WHEN** 用户成功登录或使用 refresh token 刷新
- **THEN** 后端生成 refresh token，内容为加密后的 payload：
  - `sub`: GitHub 用户 ID（字符串）
  - `iat`: 签发时间戳
  - `type`: `"refresh"`
- **AND** refresh token 使用根密钥（root secret）进行对称加密（HS256 或 AES）
- **AND** refresh token 长期有效，无过期时间

### Requirement: JWT access token validation
后端 SHALL 使用公钥验证传入的 JWT access token。

#### Scenario: Valid JWT provided
- **WHEN** 请求包含有效的 JWT（在 Authorization header 中）
- **THEN** 后端使用公钥验证签名
- **AND** 后端检查 token type 为 `"access"`
- **AND** 后端允许请求继续执行
- **AND** 后端可以从 JWT 中解析出用户 ID

#### Scenario: Expired JWT
- **WHEN** 请求包含已过期的 JWT
- **THEN** 后端返回 401 Unauthorized 错误，提示 token 过期

#### Scenario: Invalid JWT signature
- **WHEN** 请求的 JWT 签名无效（非对应私钥签发）
- **THEN** 后端返回 401 Unauthorized 错误

#### Scenario: Wrong token type
- **WHEN** 请求提供的 JWT type 不是 `"access"`
- **THEN** 后端返回 401 Unauthorized 错误

#### Scenario: Missing JWT
- **WHEN** 访问受保护端点且未提供 JWT
- **THEN** 后端返回 401 Unauthorized 错误

### Requirement: Refresh token validation
后端 SHALL 使用根密钥验证 refresh token。

#### Scenario: Valid refresh token
- **WHEN** 客户端发送 refresh token 到 `/api/auth/refresh`
- **THEN** 后端使用根密钥解密/验证 refresh token
- **AND** 后端检查 token type 为 `"refresh"`
- **AND** 从 payload 中获取 user ID
- **AND** 后端生成新的 JWT access token 和新的 refresh token

#### Scenario: Invalid refresh token
- **WHEN** 客户端发送格式错误或签名无效的 refresh token
- **THEN** 后端返回 403 Forbidden 错误
- **AND** 客户端需要重新进行 GitHub OAuth 登录

### Requirement: Token refresh endpoint
后端 SHALL 提供端点用于刷新 access token。

#### Scenario: Successful refresh
- **WHEN** 客户端发送有效的 refresh token 到 `/api/auth/refresh`
- **THEN** 后端生成新的 JWT access token（30天有效期，RS256 签名）
- **AND** 后端生成新的 refresh token（根密钥加密）
- **AND** 返回新的 access token 和 refresh token
- **AND** 旧的 refresh token 自动失效（通过 token 轮换机制）

### Requirement: Logout
前端 SHALL 支持用户退出登录。

#### Scenario: User logs out
- **WHEN** 用户点击退出登录按钮
- **THEN** 前端清除 jotai store 中的 auth 状态（自动同步到 localStorage）
- **AND** 前端重定向到 `/login` 页面

_NOTE: 后端不需要提供 logout 端点，因为认证是无状态的，token 只存储在客户端_
