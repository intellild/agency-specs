## Context

当前 P2P 模块的配置管理采用分离存储模式：

1. `authAtom` (atomWithStorage) - 存储认证信息，包含 `p2p?: P2PConfig`
2. `p2pConfigAtom` (atomWithStorage) - 独立存储 P2P 配置
3. `useP2PAuthSync` hook - 负责将 `auth.p2p` 同步到 `p2pConfigAtom`

这种设计的问题：
- 数据冗余：同一配置存储在两个地方
- 同步复杂性：需要手动维护两个 atom 的一致性
- 潜在风险：同步逻辑遗漏或时序问题导致数据不一致

## Goals / Non-Goals

**Goals:**
- 简化 P2P 配置管理，消除数据冗余
- 使用 computed atom 直接从 `authAtom` 派生配置
- 移除不必要的同步 hook (`useP2PAuthSync`)
- 保持现有功能不变

**Non-Goals:**
- 修改 P2P 连接逻辑或业务行为
- 修改认证流程
- 引入新的功能特性

## Decisions

### 使用 computed atom 替代 atomWithStorage

**决策**: 将 `p2pConfigAtom` 从 `atomWithStorage<P2PConfig | null>` 改为 `atom<P2PConfig | null>((get) => ...)`

**理由**:
- P2P 配置完全来源于认证响应，没有独立存储的必要
- computed atom 自动追踪依赖，数据一致性由 jotai 保证
- 减少本地存储使用，避免敏感配置持久化带来的安全风险

**替代方案考虑**:
- 保留现有设计但优化同步逻辑：过于复杂，没有解决根本问题
- 使用 jotai 的 `selectAtom`：没有必要，直接使用 computed atom 更简单

### 移除 useP2PAuthSync hook

**决策**: 完全移除 `useP2PAuthSync` hook 及其导出

**理由**:
- 使用 computed atom 后，配置自动同步，hook 不再需要
- 简化 P2P 模块的公共 API

**迁移策略**:
- 检查所有使用 `useP2PAuthSync` 的组件
- 移除 hook 调用，computed atom 会自动工作

## Risks / Trade-offs

**[风险] 组件可能依赖 useP2PAuthSync 的副作用**
→ 检查 `useP2PAuthSync` 的所有调用点，确认移除后不影响功能

**[风险] 某些代码可能直接写入 p2pConfigAtom**
→ 搜索所有 `set(p2pConfigAtom` 的调用，需要改为通过其他方式处理

**[权衡] 失去独立的 P2P 配置存储**
→ 当前设计下 P2P 配置本就应跟随认证，这是预期行为

## Migration Plan

1. 修改 `store.ts` 中的 `p2pConfigAtom` 定义
2. 移除 `hooks.ts` 中的 `useP2PAuthSync` hook
3. 更新 `index.ts` 中的导出列表
4. 检查并更新所有使用 `useP2PAuthSync` 的组件
5. 运行测试验证功能正常
