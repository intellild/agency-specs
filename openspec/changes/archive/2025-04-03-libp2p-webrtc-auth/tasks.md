## 1. 后端 - 依赖安装

- [x] 1.1 安装 libp2p 核心依赖: `npm install libp2p @libp2p/webrtc @libp2p/circuit-relay-v2 @libp2p/websockets @libp2p/noise @chainsafe/libp2p-yamux`
- [x] 1.2 安装类型定义: 确保 TypeScript 类型完整

## 2. 后端 - libp2p 服务器节点

- [x] 2.1 创建 `apps/server/src/p2p/node.ts` - libp2p 节点初始化模块
- [x] 2.2 配置 WebRTC 传输和监听端口
- [x] 2.3 启用 Circuit Relay v2 服务（hop 和 reservation）
- [x] 2.4 集成到 Fastify 生命周期（启动时初始化，关闭时停止）
- [x] 2.5 暴露服务器节点 ID 和 relay 地址

## 3. 后端 - P2P 鉴权

- [x] 3.1 创建 `apps/server/src/p2p/auth.ts` - P2P 连接鉴权模块
- [x] 3.2 实现 accessToken 从 Noise 握手提取和验证
- [x] 3.3 实现 Connection Gater 拦截未授权连接
- [x] 3.4 实现 Circuit Relay 预订阶段的鉴权
- [x] 3.5 实现每用户并发连接数限制
- [x] 3.6 实现 token 过期时自动断开连接

## 4. 后端 - Circuit Relay 服务

- [x] 4.1 配置 Circuit Relay v2 服务选项（预订限制、过期时间等）
- [x] 4.2 实现 relay 地址广告
- [x] 4.3 在 relay 预订时验证 accessToken
- [x] 4.4 实现每用户的 relay 预订数量限制

## 5. 后端 - 登录响应扩展

- [x] 5.1 修改登录接口，在响应中添加 `p2p` 配置字段
- [x] 5.2 包含 serverPeerId、relayAddresses、iceServers
- [x] 5.3 根据用户权限控制是否返回 P2P 配置

## 6. 前端 - 依赖安装

- [x] 6.1 安装 libp2p 客户端依赖: `npm install libp2p @libp2p/webrtc @libp2p/circuit-relay-v2 @libp2p/websockets @libp2p/noise @chainsafe/libp2p-yamux`
- [x] 6.2 配置构建工具支持 libp2p 的 WebAssembly 模块

## 7. 前端 - libp2p 客户端

- [x] 7.1 创建 `apps/client/src/p2p/client.ts` - libp2p 客户端模块
- [x] 7.2 实现登录后初始化 libp2p 节点
- [x] 7.3 实现通过 Circuit Relay 预订连接到服务器
- [x] 7.4 在 relay 电路上交换 WebRTC SDP offer/answer
- [x] 7.5 在 relay 电路上交换 ICE candidates
- [x] 7.6 建立 WebRTC 直连后关闭 relay 电路
- [x] 7.7 添加浏览器兼容性检查

## 8. 前端 - P2P 状态管理

- [x] 8.1 创建 P2P 连接状态 store（使用现有状态管理方案）
- [x] 8.2 实现连接状态追踪：relay-connecting/signal-exchanging/connected/disconnected
- [x] 8.3 实现断线重连逻辑（指数退避）
- [x] 8.4 实现 WebRTC 失败时 fallback 到 Circuit Relay
- [x] 8.5 暴露 P2P 连接状态给 UI 组件

## 9. 集成测试

- [x] 9.1 测试完整登录 + Circuit Relay 预订 + WebRTC 连接流程
- [x] 9.2 测试无效 token 被拒绝的场景（relay 预订阶段）
- [x] 9.3 测试连接数限制
- [x] 9.4 测试 token 过期后连接断开
- [x] 9.5 测试 WebRTC 失败时的 relay fallback

## 10. 文档和配置

- [x] 10.1 更新环境变量配置文档（P2P 端口、relay 限制等）
- [x] 10.2 添加 P2P 相关配置到 `.env.example`
