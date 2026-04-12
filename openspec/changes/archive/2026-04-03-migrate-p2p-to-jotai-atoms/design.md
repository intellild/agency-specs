## Context

The current P2P implementation uses a class-based `P2PClient` that manages:
- Libp2p node instance
- Connection state
- Reconnection logic via internal timers
- State change listeners

This class maintains internal mutable state and uses event listeners for state propagation, which doesn't align with the jotai-based reactive architecture used elsewhere in the app.

## Goals / Non-Goals

**Goals:**
- Replace `P2PClient` class with jotai atoms for all state management
- Maintain identical external behavior in `useP2P()` hook
- Support auto-connect after login via atom effects
- Enable better testability through pure functions
- Preserve all existing connection states and reconnection logic

**Non-Goals:**
- Changing the libp2p protocol or connection flow
- Modifying UI components that use `useP2P()`
- Adding new P2P features beyond state management refactor

## Decisions

### 1. Use `atom` for libp2p node instance
**Decision**: Store the libp2p node instance in a standard `atom<Libp2p | null>`.

**Rationale**: Libp2p instances are not serializable and shouldn't be persisted. A standard atom keeps it in memory only.

**Alternative considered**: `atomWithStorage` - rejected because libp2p instances can't be serialized.

### 2. Use `atomWithLazy` for connection logic
**Decision**: Create a lazy atom that initializes connection when first read.

**Rationale**: Delays libp2p initialization until actually needed, improving initial page load.

### 3. Keep reconnection logic in effect atoms
**Decision**: Use `atomEffect` for reconnection scheduling.

**Rationale**: Reconnection involves side effects (timers) and should be handled by jotai effects, not in the store directly.

### 4. Preserve existing store.ts exports
**Decision**: Keep existing atom exports in `store.ts` backward compatible.

**Rationale**: Some components may import atoms directly; we want to minimize breaking changes.

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| Memory leaks from libp2p instances | Ensure cleanup in atom onUnmount effects |
| Race conditions during reconnection | Use atom-family or single source of truth for connection state |
| Breaking changes for direct `P2PClient` imports | Remove exports from index.ts, document migration path |
| Complex async cleanup | Implement proper disconnect cleanup in useP2P hook unmount |

## Migration Plan

1. Create new atoms for libp2p instance management
2. Convert P2PClient methods to pure functions
3. Refactor hooks.ts to use atoms
4. Update index.ts exports
5. Test all connection scenarios (connect, disconnect, reconnect, error)
6. Remove client.ts
