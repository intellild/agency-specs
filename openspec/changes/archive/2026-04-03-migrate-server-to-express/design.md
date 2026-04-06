## Context

当前后端 (`agency/apps/server`) 使用 Fastify 5 框架，包含以下核心组件：
- **入口**: `main.ts` - Fastify 实例创建、CORS 配置、P2P 初始化
- **应用**: `app.ts` - 使用 `@fastify/autoload` 自动加载插件和路由
- **插件**: JWT 认证插件 (`jwt.ts`)、错误处理 (`sensible.ts`)
- **路由**: GitHub OAuth (`auth/index.ts`)、受保护路由 (`protected/index.ts`)、根路由 (`root.ts`)
- **P2P**: 独立的 P2P 节点管理模块

项目已安装 Express 依赖但未使用，迁移可以复用现有业务逻辑。

## Goals / Non-Goals

**Goals:**
- 将后端框架从 Fastify 迁移至 Express 4.x
- 保持现有 API 接口行为不变（路由路径、请求/响应格式）
- 复用现有业务逻辑（JWT 生成/验证、GitHub OAuth、P2P 集成）
- 简化项目结构，移除 Fastify 特有的插件和装饰器机制
- 保留 TypeScript 类型安全

**Non-Goals:**
- 不修改数据库或存储层逻辑
- 不修改 P2P 核心实现（仅调整集成方式）
- 不修改前端代码或 API 契约
- 不引入新的业务功能

## Decisions

### 1. 使用原生 Express 中间件替代 Fastify 插件
**Rationale**: Express 中间件机制是标准 Node.js 模式，更简洁且无需额外抽象。
- Fastify 的 `decorate` → Express 的 `req.user` 属性扩展
- Fastify 的 `preHandler` → Express 的中间件链

### 2. 手动路由注册替代 autoload
**Rationale**: 项目路由数量少（3 个主路由），手动注册更清晰，减少魔法。
```typescript
// 新结构
app.use('/auth', authRoutes);
app.use('/api', protectedRoutes);
```

### 3. 使用 `cors` 包处理跨域
**Rationale**: Express 生态有成熟的 `cors` 中间件，配置方式与 Fastify CORS 类似。

### 4. JWT 认证中间件实现
**Rationale**: 将 Fastify 的 `authenticate` 装饰器转换为标准 Express 中间件：
```typescript
export const authenticate: RequestHandler = async (req, res, next) => {
  // 验证逻辑...
  req.user = payload;
  next();
};
```

### 5. 保留 Zod 用于运行时验证
**Rationale**: 虽然移除了 `fastify-type-provider-zod`，但 Zod 仍可独立使用进行请求体验证。

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| Express 性能低于 Fastify | 当前应用为内部工具，请求量低，性能差异可忽略 |
| 类型安全降级（无 type-provider） | 在 handler 内手动使用 Zod 验证，保持运行时安全 |
| 中间件错误处理差异 | 统一使用 `asyncHandler` 包装或 try-catch 捕获 |
| P2P 生命周期管理 | 将 P2P 初始化移至 `app.listen` 回调，关闭处理使用 `process.on('SIGTERM')` |

## Migration Plan

1. **准备阶段**: 更新 `package.json` 依赖（添加 `cors`, `@types/cors`）
2. **核心迁移**: 重写 `main.ts` 创建 Express 应用
3. **中间件迁移**: 将 JWT 插件转换为 Express 中间件
4. **路由迁移**: 重写所有路由文件为 Express Router
5. **类型迁移**: 更新类型声明文件
6. **验证**: 启动服务，测试所有端点
7. **清理**: 移除 Fastify 相关依赖

## Open Questions

- 是否需要引入 `express-async-handler` 简化异步路由错误处理？（建议：是，减少样板代码）
