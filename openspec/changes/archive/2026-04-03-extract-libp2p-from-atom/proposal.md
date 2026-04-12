## Why

Storing the libp2p instance in a jotai atom (`libp2pNodeAtom`) is problematic because libp2p instances are complex, non-serializable objects with internal state, event listeners, and connections. This causes issues with React DevTools, potential memory leaks, and unnecessary re-renders when the atom changes. State management libraries like jotai should only hold serializable state, not live object instances.

## What Changes

- **Remove** `libp2pNodeAtom` and `serverConnectionAtom` from jotai store
- **Introduce** a module-level variable to hold the libp2p instance outside of jotai
- **Create** accessor functions (`getLibp2pNode()`, `setLibp2pNode()`) for the instance
- **Update** all code that reads/writes these atoms to use the new accessor pattern
- **Update** `connectP2PAtom`, `disconnectP2PAtom`, `resetP2PAtom` to use the new module-level storage
- **Update** `p2pConnectionEffect` to reference the instance via accessor functions
- **Update** exports in `p2p/index.ts` to remove atom exports and add new accessors

## Capabilities

### New Capabilities
- `p2p-instance-management`: Manage libp2p node instance outside of jotai state using module-level storage with accessor functions

### Modified Capabilities
- `p2p-libp2p-atoms`: Remove libp2p node instance and server connection from atom storage (implementation change only, no requirement changes)

## Impact

- `agency/apps/client/src/p2p/store.ts` - Major refactor of instance storage
- `agency/apps/client/src/p2p/index.ts` - Update exports
- `agency/apps/client/src/p2p/hooks.ts` - May need updates if hooks directly access atoms
- Any components importing `libp2pNodeAtom` or `serverConnectionAtom` will need updates
