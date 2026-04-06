## ADDED Requirements

### Requirement: Frontend initiates GitHub OAuth login
前端 SHALL 直接构建 GitHub OAuth 授权 URL 并跳转。

#### Scenario: User opens login page
- **WHEN** 用户访问 `/login`
- **THEN** 页面显示服务器地址输入框，默认值为 `http://localhost:3000`
- **AND** 页面显示 GitHub Client ID 输入框
- **AND** 页面显示"使用 GitHub 登录"按钮

#### Scenario: User initiates login
- **WHEN** 用户点击"使用 GitHub 登录"按钮
- **THEN** 前端生成随机 state 参数并保存到 sessionStorage
- **AND** 前端构建 GitHub OAuth URL（`https://github.com/login/oauth/authorize`）
- **AND** URL 包含 client_id、redirect_uri、state 和 scope 参数
- **AND** 浏览器跳转到 GitHub OAuth 授权页面

### Requirement: Handle OAuth callback
后端 SHALL 提供 POST 端点处理 GitHub OAuth 回调并交换授权码。

#### Scenario: Successful OAuth callback
- **WHEN** 前端从 GitHub 回调中获取 code 和 state 参数
- **AND** 前端验证 state 参数与 sessionStorage 中保存的一致
- **AND** 前端发送 POST 请求到 `/auth/github/callback`，body 包含 code
- **THEN** 后端使用授权码向 GitHub 请求 access_token
- **AND** 后端使用 access_token 获取用户 GitHub 信息
- **AND** 后端生成包含 GitHub 用户 ID 的 JWT access token
- **AND** 后端生成 refresh token
- **AND** 后端返回 JSON 响应，包含 accessToken、refreshToken、githubToken、userId、username

#### Scenario: Invalid state parameter
- **WHEN** 前端验证 state 参数与 sessionStorage 中保存的不匹配
- **THEN** 前端拒绝处理，显示登录失败错误

#### Scenario: GitHub returns error
- **WHEN** GitHub 回调包含 error 参数（用户拒绝授权）
- **THEN** 前端显示登录失败错误信息
