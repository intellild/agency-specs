## Context

当前系统使用 Fastify 后端和 Modern.js 前端，登录后所有通信都通过 HTTP/WebSocket 经过服务器中转。为了实现客户端和服务器之间的直接 P2P 通信，需要引入 libp2p WebRTC 传输。

### 当前架构
- 后端: Fastify 5 + TypeScript
- 前端: Modern.js 3 + React 19
- 认证: JWT accessToken

### 约束
- 客户端运行在浏览器中，需要支持 WebRTC
- 服务器运行在 Node.js 环境
- 必须保持现有的 accessToken 鉴权机制

## Goals / Non-Goals

**Goals:**
- 服务器启用 libp2p 节点，监听 WebRTC 连接
- 服务器启用 Circuit Relay 服务协助信令交换
- 客户端登录后能通过 Circuit Relay 完成信令，然后通过 WebRTC 直连服务器
- 服务器对每次 P2P 连接进行 accessToken 鉴权

**Non-Goals:**
- 客户端之间的 P2P 通信（仅客户端-服务器）
- 替换现有的 HTTP API（P2P 作为补充通道）
- 支持非浏览器的客户端

## Decisions

### 1. 使用 libp2p 的 WebRTC 传输
**选择**: 使用 `@libp2p/webrtc` 传输
**理由**: 
- libp2p 提供完整的 P2P 协议栈
- WebRTC 传输支持浏览器环境
- 内置 NAT 穿透能力

**替代方案考虑**:
- 原生 WebRTC: 需要自行处理信令和协议，复杂度高
- WebSocket: 不能实现真正的 P2P，仍需服务器中转

### 2. 使用 Circuit Relay 进行信令
**选择**: 使用 `@libp2p/circuit-relay-v2` 协议处理 WebRTC 握手信令
**理由**:
- 无需自建 WebSocket 信令服务器，简化架构
- Circuit Relay 是 libp2p 标准协议，与 WebRTC 传输集成良好
- 信令数据通过加密的 libp2p 连接传输，更安全
- 客户端先通过 Circuit Relay 与服务器建立虚拟连接，协商 WebRTC SDP

**工作流程**:
1. 客户端通过 WebSocket 或 WebRTC 直连到服务器的 Relay 服务
2. 客户端通过 Circuit Relay 协议与目标服务器节点建立虚拟电路
3. 在电路上交换 WebRTC SDP offer/answer
4. 完成 WebRTC 直连后，关闭 Relay 电路（或使用直连传输）

### 3. 鉴权时机：连接建立时
**选择**: 在 libp2p 连接建立时进行 accessToken 验证
**理由**:
- 在协议层拦截，早于应用层处理
- 可以利用 libp2p 的 connection gating 机制
- 鉴权失败立即断开连接，不暴露后续服务

**实现方式**:
- 自定义 Connection Gater，在 `denyInboundConnection` 或协议握手阶段验证
- 使用 Noise 协议传输 accessToken
- 对于 Circuit Relay，在 reservation 阶段验证客户端身份

### 4. 登录响应扩展
**选择**: 登录成功后返回 libp2p 连接配置
**理由**:
- 客户端需要知道服务器节点 ID 和 Relay 地址
- 可以在登录时一并验证 P2P 连接权限
- 避免额外的配置请求

## Risks / Trade-offs

| 风险 | 缓解措施 |
|------|----------|
| WebRTC 连接失败率较高（防火墙/NAT） | 实现优雅降级，失败时回退到 HTTP/WebSocket 或保持 Circuit Relay 连接 |
| Circuit Relay 增加服务器负载 | 仅用于信令阶段，WebRTC 直连成功后关闭 |
| libp2p 增加包体积 | 仅加载必要的传输模块，前端使用动态导入 |
| 鉴权逻辑复杂化 | 封装为独立模块，保持与现有 auth 中间件一致 |
| 浏览器兼容性 | 要求现代浏览器（Chrome 80+, Firefox 75+, Safari 14+） |

## Migration Plan

### Phase 1: 服务器端 libp2p 集成
1. 安装 libp2p 依赖（webrtc, circuit-relay-v2）
2. 配置 WebRTC 传输和监听
3. 启用 Circuit Relay 服务
4. 实现连接鉴权中间件

### Phase 2: 客户端集成
1. 安装 libp2p 客户端库
2. 实现通过 Circuit Relay 连接服务器
3. 在 Relay 电路上交换 WebRTC SDP
4. 建立 WebRTC 直连
5. 添加连接状态管理

### Phase 3: 测试和优化
1. 测试 Circuit Relay + WebRTC 连接流程
2. 测试各种网络环境下的连接成功率
3. 实现连接失败降级

## Open Questions

1. 是否需要支持多服务器部署时的节点发现？
2. P2P 连接的超时和保活策略？
3. 是否需要限制单个用户的并发 P2P 连接数？
4. 是否保留 Circuit Relay 作为 WebRTC 失败后的备用通道？
