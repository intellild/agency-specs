## Why

登录后，客户端和服务器需要建立直接的 P2P 通信通道以实现实时数据传输，避免所有流量都经过服务器中转，降低延迟和服务器负载。libp2p 的 WebRTC 传输是实现浏览器端 P2P 连接的最佳方案。

## What Changes

- 后端（Fastify）集成 libp2p，启用 WebRTC 传输监听
- 后端启用 Circuit Relay 服务，用于协助客户端进行信令交换和 NAT 穿透
- 后端实现连接鉴权中间件，验证客户端 accessToken
- 前端（Modern.js/React）集成 libp2p，通过 Circuit Relay 和 WebRTC 连接到服务器
- 登录流程返回 libp2p 连接所需的凭证和节点信息

## Capabilities

### New Capabilities
- `libp2p-webrtc-server`: 服务器端 libp2p 节点配置和 WebRTC 监听
- `libp2p-webrtc-client`: 客户端 libp2p 连接和信令处理
- `p2p-auth`: P2P 连接接入鉴权，使用 accessToken 验证客户端身份
- `circuit-relay`: Circuit Relay 服务，协助 WebRTC 握手和信令传递

### Modified Capabilities
- `auth-login`: 扩展登录响应，包含 libp2p 连接凭证（节点 ID、relay 地址等）

## Impact

- **后端**: 新增 libp2p 依赖，需要配置 WebRTC 传输和 Circuit Relay 服务
- **前端**: 新增 libp2p 客户端库，登录后通过 Circuit Relay 建立 P2P 连接
- **API**: 无需新增 HTTP 端点，信令通过 libp2p Circuit Relay 协议处理
- **依赖**: 新增 `@libp2p/webrtc`, `@libp2p/circuit-relay` 等包
- **部署**: 需要开放 WebRTC 所需的 UDP 端口范围
