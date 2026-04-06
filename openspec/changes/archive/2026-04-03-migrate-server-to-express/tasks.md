## 1. 依赖管理

- [x] 1.1 更新 `agency/apps/server/package.json`，添加 Express 相关依赖（express, cors, @types/express, @types/cors）
- [x] 1.2 移除 Fastify 相关依赖（fastify, @fastify/*, fastify-type-provider-zod, fastify-cli）
- [x] 1.3 安装新依赖（pnpm install）

## 2. 核心服务器迁移

- [x] 2.1 重写 `src/main.ts`，使用 Express 创建应用实例，配置 CORS 和 JSON 解析
- [x] 2.2 配置服务器启动和关闭钩子（P2P 初始化和停止）
- [x] 2.3 删除 `src/app/app.ts`（Fastify 的 autoload 不再需要）

## 3. 中间件实现

- [x] 3.1 创建 `src/middleware/jwt.ts`，实现 Express JWT 认证中间件
- [x] 3.2 更新 `src/types/express.d.ts`（或 `src/types/index.ts`），扩展 Express Request 类型添加 `user` 属性
- [x] 3.3 删除 `src/app/plugins/jwt.ts` 和 `src/app/plugins/sensible.ts`
- [x] 3.4 删除 `src/types/fastify.d.ts`

## 4. 路由迁移

- [x] 4.1 创建 `src/routes/auth.ts`，将 GitHub OAuth 逻辑从 Fastify 路由转换为 Express Router
- [x] 4.2 创建 `src/routes/protected.ts`，将受保护路由转换为 Express Router
- [x] 4.3 创建 `src/routes/root.ts`，将根路由转换为 Express Router
- [x] 4.4 删除 `src/app/routes/` 目录及其内容

## 5. P2P 集成调整

- [x] 5.1 调整 `src/p2p/index.ts` 导出，确保与 Express 集成兼容
- [x] 5.2 更新 `src/p2p/node.ts` 中的服务器日志调用（如果需要）

## 6. 项目结构调整

- [x] 6.1 更新 `tsconfig.app.json` 确保新的文件路径被包含
- [x] 6.2 删除不再需要的 `src/app/` 目录
- [x] 6.3 更新 `.env.example`（如果需要）

## 7. 清理

- [x] 7.1 运行代码检查（biome check）
- [x] 7.2 清理任何残留的 Fastify 配置文件
