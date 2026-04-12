## 1. Create libp2p utility functions

- [x] 1.1 Create `createLibp2pNode(config)` function in `client.ts` to initialize libp2p instance
- [x] 1.2 Create `isLibp2pSupported()` and `isWebRTCSupported()` helper functions (keep from existing)
- [x] 1.3 Create `dialServer(libp2p, config)` function to handle connection logic
- [x] 1.4 Create `attemptWebRTCDirect(libp2p, serverPeerId)` function for direct WebRTC connection
- [x] 1.5 Export types `P2PConfig`, `ConnectionState`, `P2PConnection`

## 2. Enhance store.ts with libp2p atoms

- [x] 2.1 Add `libp2pNodeAtom` (atom<Libp2p | null>) for storing libp2p instance
- [x] 2.2 Add `serverConnectionAtom` (atom<Connection | null>) for server connection
- [x] 2.3 ~~Add `p2pAccessTokenAtom`~~ (removed - use `getAccessToken()` from `@/stores/auth` instead)
- [x] 2.4 Add `p2pReconnectAttemptsAtom` (atom<number>) for tracking reconnection count
- [x] 2.5 Add `p2pReconnectTimerAtom` (atom<ReturnType<typeof setTimeout> | null>) for timer cleanup

## 3. Create P2P connection action atoms

- [x] 3.1 Create `connectP2PAtom` (atom(null, async (get, set) => {...})) for connection logic
- [x] 3.2 Create `disconnectP2PAtom` (atom(null, async (get, set) => {...})) for disconnection cleanup
- [x] 3.3 Create `resetP2PAtom` (atom(null, (get, set) => {...})) for full reset

## 4. Create P2P auto-connect effect atom

- [x] 4.1 Create `p2pAutoConnectEffect` using `atomEffect` that watches auth and config atoms
- [x] 4.2 Add logic to trigger connection when auth is present, config is valid, and autoConnect is enabled
- [x] 4.3 Add logic to disconnect when auth becomes null (logout)

## 5. Create reconnection effect atom

- [x] 5.1 Create `p2pReconnectEffect` using `atomEffect` that watches connection state
- [x] 5.2 Implement exponential backoff logic (base delay 1000ms, max 5 attempts)
- [x] 5.3 Update `p2pReconnectStateAtom` with attempt count and next delay
- [x] 5.4 Add max attempts check and error state handling

## 6. Refactor hooks.ts

- [x] 6.1 Remove `useRef<P2PClient>` and all P2PClient references
- [x] 6.2 Update `useP2P()` to use `useSetAtom(connectP2PAtom)` and `useSetAtom(disconnectP2PAtom)`
- [x] 6.3 Update `useP2PAuthSync()` to set config from auth atom and trigger auto-connect effect
- [x] 6.4 Add `useEffect` for cleanup on unmount

## 7. Update exports in index.ts

- [x] 7.1 Remove `P2PClient`, `getP2PClient`, `resetP2PClient` exports
- [x] 7.2 Keep type exports for backward compatibility
- [x] 7.3 Export new action atoms if needed by external code

## 8. Test and verify

- [x] 8.1 Verify successful connection after login
- [x] 8.2 Verify disconnection on logout
- [x] 8.3 Verify reconnection after network interruption
- [x] 8.4 Verify max reconnection attempts handling
- [x] 8.5 Verify browser support detection works
- [x] 8.6 Run existing tests if available
