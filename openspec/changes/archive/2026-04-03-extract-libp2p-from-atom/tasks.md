## 1. Add Module-Level Instance Storage

- [x] 1.1 Add module-level `libp2pNodeInstance` variable in `agency/apps/client/src/p2p/store.ts`
- [x] 1.2 Add module-level `serverConnectionInstance` variable in `agency/apps/client/src/p2p/store.ts`
- [x] 1.3 Add `getLibp2pNode()` accessor function
- [x] 1.4 Add `setLibp2pNode()` accessor function
- [x] 1.5 Add `getServerConnection()` accessor function
- [x] 1.6 Add `setServerConnection()` accessor function

## 2. Update Connection Actions

- [x] 2.1 Update `connectP2PAtom` to use `getLibp2pNode()` instead of `get(libp2pNodeAtom)`
- [x] 2.2 Update `connectP2PAtom` to use `setLibp2pNode()` instead of `set(libp2pNodeAtom, ...)`
- [x] 2.3 Update `connectP2PAtom` to use `setServerConnection()` instead of `set(serverConnectionAtom, ...)`
- [x] 2.4 Update `disconnectP2PAtom` to use `getLibp2pNode()` and `getServerConnection()`
- [x] 2.5 Update `disconnectP2PAtom` to use `setLibp2pNode(null)` and `setServerConnection(null)`
- [x] 2.6 Update `resetP2PAtom` to use setter functions instead of atom set

## 3. Remove Instance Atoms

- [x] 3.1 Remove `libp2pNodeAtom` export from `agency/apps/client/src/p2p/store.ts`
- [x] 3.2 Remove `serverConnectionAtom` export from `agency/apps/client/src/p2p/store.ts`
- [x] 3.3 Remove atom definitions from store.ts (keep internally if needed for transition)

## 4. Update Public API

- [x] 4.1 Remove `libp2pNodeAtom` export from `agency/apps/client/src/p2p/index.ts`
- [x] 4.2 Remove `serverConnectionAtom` export from `agency/apps/client/src/p2p/index.ts`
- [x] 4.3 Add `getLibp2pNode` export to `agency/apps/client/src/p2p/index.ts`
- [x] 4.4 Add `setLibp2pNode` export to `agency/apps/client/src/p2p/index.ts`
- [x] 4.5 Add `getServerConnection` export to `agency/apps/client/src/p2p/index.ts`
- [x] 4.6 Add `setServerConnection` export to `agency/apps/client/src/p2p/index.ts`

## 5. Verify and Test

- [x] 5.1 Check for any other files importing `libp2pNodeAtom` or `serverConnectionAtom`
- [x] 5.2 Update any found references to use new accessors
- [x] 5.3 Run TypeScript compilation to verify no type errors
- [x] 5.4 Run tests to verify P2P functionality still works
