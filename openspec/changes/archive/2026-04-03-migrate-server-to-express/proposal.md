## Why

当前后端使用 Fastify 框架，但团队更熟悉 Express 生态，且 Express 的中间件生态更丰富、社区支持更广泛。迁移到 Express 可以降低维护成本，简化项目结构，并提高开发效率。

## What Changes

- **BREAKING**: 将 Fastify 后端框架迁移至 Express
  - 移除 `@fastify/*` 相关依赖及插件体系
  - 使用 Express 中间件替代 Fastify 插件机制
  - 路由层改用 Express Router 实现
- 简化项目结构，统一代码风格
- 保留现有业务逻辑（GitHub OAuth、JWT 认证、P2P 功能）
- 更新构建配置和开发脚本

## Capabilities

### New Capabilities
- `express-server-core`: Express 服务器核心功能，包括应用实例创建、中间件注册、路由挂载
- `jwt-auth-middleware`: JWT 认证中间件，替代 Fastify 的 `authenticate` 装饰器
- `github-oauth-routes`: GitHub OAuth 回调和 Token 刷新路由
- `p2p-integration`: P2P 节点初始化和生命周期管理集成

### Modified Capabilities
<!-- 无现有 spec 需要修改 -->

## Impact

- **依赖**: 移除 `fastify`, `@fastify/*`, `fastify-type-provider-zod`, `fastify-cli`, `fastify-plugin`；新增 `express`, `cors`, `@types/express`
- **代码**: `agency/apps/server/src/` 下的所有源文件需要重写
- **构建**: `package.json` scripts 和 Nx 配置可能需要调整
- **类型**: Fastify 类型声明迁移至 Express 类型声明
