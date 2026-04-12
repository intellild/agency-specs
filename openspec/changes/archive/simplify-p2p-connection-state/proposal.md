## Why

The current `p2pConnectionStateAtom` has an `enabled` field that tracks browser support for libp2p features, and there's a separate `p2pAutoConnectAtom` for controlling auto-connect behavior. These add unnecessary complexity since:
1. The `enabled` field duplicates what can be checked at runtime via `isLibp2pSupported()`
2. Auto-connect behavior should be the default and doesn't need a separate toggle

Simplifying the state management will make the code easier to maintain and understand.

## What Changes

- **BREAKING**: Remove `enabled` field from `P2PConnectionState` interface and `p2pConnectionStateAtom`
- **BREAKING**: Remove `p2pAutoConnectAtom` and its localStorage persistence
- **BREAKING**: Remove auto-connect toggle button from Dashboard UI
- Remove `p2pAutoConnectEffect` auto-connect logic (keep disconnect-on-logout behavior)
- Update `p2pStatusAtom.canConnect` to not depend on `enabled`
- Update `useP2P` hook to remove autoConnect-related exports
- Update `p2pInitEffect` to remove `enabled` state initialization
- Clean up imports and exports in `p2p/index.ts`

## Capabilities

### New Capabilities
- None

### Modified Capabilities
- None (implementation simplification only, no behavioral spec changes)

## Impact

- `agency/apps/client/src/p2p/store.ts` - Core state management changes
- `agency/apps/client/src/p2p/hooks.ts` - Hook API changes
- `agency/apps/client/src/p2p/index.ts` - Export changes
- `agency/apps/client/src/components/dashboard.tsx` - UI removal of auto-connect toggle
