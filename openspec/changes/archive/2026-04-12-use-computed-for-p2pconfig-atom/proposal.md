## Why

当前的 `p2pConfigAtom` 使用 `atomWithStorage` 独立存储 P2P 配置，需要通过 `useP2PAuthSync` hook 手动从 `authAtom` 同步数据。这种设计存在数据不一致的风险，且增加了不必要的复杂性。由于 P2P 配置本就来自认证响应，直接使用 computed atom 从 `authAtom` 派生更加简洁可靠。

## What Changes

- 将 `p2pConfigAtom` 从 `atomWithStorage` 改为 computed `atom`
- 直接从 `authAtom` 派生 P2P 配置，消除手动同步
- 移除 `useP2PAuthSync` hook（不再需要）
- 更新相关代码中的导入和使用方式

## Capabilities

### New Capabilities
- （无新功能，仅为代码重构）

### Modified Capabilities
- （无行为规范变更，仅为实现方式优化）

## Impact

- `apps/client/src/p2p/store.ts` - 修改 `p2pConfigAtom` 定义
- `apps/client/src/p2p/hooks.ts` - 移除 `useP2PAuthSync` hook
- `apps/client/src/p2p/index.ts` - 更新导出列表
- 可能影响使用 `useP2PAuthSync` 的组件
