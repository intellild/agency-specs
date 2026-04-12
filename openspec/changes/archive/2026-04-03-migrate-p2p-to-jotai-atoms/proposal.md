## Why

The current P2P implementation uses a class-based `P2PClient` with internal state and event listeners, mixing state management with business logic. This makes the code harder to test, maintain, and reason about. Migrating all state to Jotai atoms provides a more declarative, reactive, and testable architecture that aligns with the existing jotai-based auth system.

## What Changes

- **BREAKING**: Remove `P2PClient` class and its singleton pattern from `client.ts`
- **BREAKING**: Remove `getP2PClient()` and `resetP2PClient()` exports
- Convert all P2P connection logic to pure functions
- Migrate all P2P state management to Jotai atoms (existing atoms in `store.ts` will be enhanced)
- Add new atoms for libp2p node instance and connection management
- Update `useP2P` hook to use atoms exclusively, removing `useRef` for client instance
- Keep the same public hook API for backward compatibility in components
- Update `hooks.ts` to handle auto-connect after login using jotai atoms

## Capabilities

### New Capabilities
- `p2p-libp2p-atoms`: Jotai atoms for managing libp2p node lifecycle, connection state, and reconnection logic

### Modified Capabilities
- `p2p-connection-management`: Existing connection management will be refactored from class-based to atom-based without changing external behavior

## Impact

- `agency/apps/client/src/p2p/client.ts` - Removed entirely or simplified to utility functions
- `agency/apps/client/src/p2p/store.ts` - Enhanced with new atoms for libp2p instance management
- `agency/apps/client/src/p2p/hooks.ts` - Refactored to use atoms instead of P2PClient class
- `agency/apps/client/src/p2p/index.ts` - Updated exports
- Components using `useP2P()` hook - No changes needed (API preserved)
