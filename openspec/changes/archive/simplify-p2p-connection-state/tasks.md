## 1. Update P2P Store (store.ts)

- [x] 1.1 Remove `enabled` field from `P2PConnectionState` interface
- [x] 1.2 Remove `enabled: false` from `initialConnectionState`
- [x] 1.3 Remove `p2pAutoConnectAtom` atomWithStorage
- [x] 1.4 Update `p2pStatusAtom` - remove `enabled` from `canConnect` check and destructuring
- [x] 1.5 Update `connectP2PAtom` - remove `enabled` check (keep other validations)
- [x] 1.6 Update `p2pAutoConnectEffect` - remove `enabled` and `autoConnect` checks, rename to `p2pConnectionEffect`
- [x] 1.7 Update `p2pInitEffect` - remove `enabled` state setting logic (can remove entire effect if not needed)

## 2. Update P2P Hooks (hooks.ts)

- [x] 2.1 Remove `p2pAutoConnectAtom` import
- [x] 2.2 Remove `autoConnect` and `setAutoConnect` from hook
- [x] 2.3 Remove `useAtom(p2pAutoConnectAtom)` call
- [x] 2.4 Remove `enabled` setting from useEffect (remove entire effect if not needed)
- [x] 2.5 Remove `autoConnect` and `setAutoConnect` from return object
- [x] 2.6 Remove `p2pAutoConnectEffect` import and usage (use renamed `p2pConnectionEffect`)

## 3. Update P2P Index (index.ts)

- [x] 3.1 Remove `p2pAutoConnectAtom` export
- [x] 3.2 Update `p2pAutoConnectEffect` export to `p2pConnectionEffect` (if renamed)

## 4. Update Dashboard UI (dashboard.tsx)

- [x] 4.1 Remove `autoConnect` and `setAutoConnect` from useP2P destructuring
- [x] 4.2 Remove auto-connect toggle Button component

## 5. Verify and Test

- [x] 5.1 Run type check to ensure no TypeScript errors
- [x] 5.2 Verify P2P connection still works correctly
- [x] 5.3 Verify disconnect-on-logout still works
