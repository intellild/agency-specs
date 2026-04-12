## Context

The current P2P implementation stores the live libp2p node instance and server connection in jotai atoms (`libp2pNodeAtom` and `serverConnectionAtom`). This is an anti-pattern because:

1. **Non-serializable state**: libp2p instances contain circular references, event listeners, and WebSocket connections that cannot be serialized
2. **React DevTools issues**: jotai atoms with non-serializable data cause warnings and inspection problems
3. **Memory leak potential**: The atom holding a reference prevents garbage collection even when the instance is stopped
4. **Unnecessary re-renders**: Any update to the atom (even the same instance) triggers subscriber re-renders

The current code at `agency/apps/client/src/p2p/store.ts` lines 54-58:
```typescript
// Libp2p node instance
export const libp2pNodeAtom = atom<Libp2p | null>(null);

// Server connection
export const serverConnectionAtom = atom<Connection | null>(null);
```

## Goals / Non-Goals

**Goals:**
- Remove libp2p instance and server connection from jotai atoms
- Store these instances in module-level variables with accessor functions
- Maintain the same external API for connection/disconnection actions
- Preserve all existing functionality (auto-connect, reconnection, cleanup)

**Non-Goals:**
- Changing connection logic or protocol handling
- Adding new P2P features
- Refactoring the connection state machine
- Modifying the hooks API (except removing atom exports)

## Decisions

### Decision: Use module-level variables with accessor functions

**Rationale**: Module-level variables provide singleton-like storage without the serialization overhead of jotai. Accessor functions provide controlled access and allow for future extension (e.g., debug logging, validation).

**Alternative considered**: React Context - rejected because it would still require the instance to flow through React's rendering system, causing similar issues.

**Implementation**:
```typescript
// Module-level storage
let libp2pNodeInstance: Libp2p | null = null;
let serverConnectionInstance: Connection | null = null;

// Accessor functions
export function getLibp2pNode(): Libp2p | null { return libp2pNodeInstance; }
export function setLibp2pNode(node: Libp2p | null): void { libp2pNodeInstance = node; }
export function getServerConnection(): Connection | null { return serverConnectionInstance; }
export function setServerConnection(conn: Connection | null): void { serverConnectionInstance = conn; }
```

### Decision: Keep connection STATE in jotai atoms

**Rationale**: Connection state (`state`, `error`, `info`) is serializable and should remain in jotai for reactive UI updates. Only the live instances need to be extracted.

**Implementation**: `p2pConnectionStateAtom` remains unchanged, but references to `libp2pNodeAtom` and `serverConnectionAtom` are replaced with accessor function calls.

### Decision: Update atom actions to use accessors

**Rationale**: The `connectP2PAtom`, `disconnectP2PAtom`, and `resetP2PAtom` action atoms currently read/write the instance atoms. They need to be updated to use the accessor functions.

**Changes needed**:
- `get(libp2pNodeAtom)` → `getLibp2pNode()`
- `set(libp2pNodeAtom, value)` → `setLibp2pNode(value)`
- Same pattern for `serverConnectionAtom`

## Risks / Trade-offs

**[Risk]** Module-level state makes testing harder (no easy reset between tests) → **Mitigation**: The `resetP2PAtom` action and accessor functions provide a clean way to reset state in tests

**[Risk]** Accessor functions add a thin abstraction layer → **Mitigation**: This is minimal overhead and provides flexibility for future changes

**[Risk]** Components/hooks importing `libp2pNodeAtom` directly will break → **Mitigation**: This is intentional - direct atom access was the anti-pattern we're fixing

## Migration Plan

1. Create module-level variables and accessor functions in `store.ts`
2. Update all internal references in `store.ts` (actions, effects) to use accessors
3. Remove `libp2pNodeAtom` and `serverConnectionAtom` exports from `index.ts`
4. Add new accessor exports to `index.ts`
5. Search for any other files importing the removed atoms and update them
6. Run tests to verify functionality
