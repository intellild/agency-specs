## 1. Server - Create P2P Config Endpoint

- [x] 1.1 Create new route file `apps/server/src/routes/p2p.ts` with GET `/config` endpoint
- [x] 1.2 Implement JWT authentication middleware for the endpoint
- [x] 1.3 Return P2P config using existing `getP2PNodeInfo()` and `getICEServers()` functions
- [x] 1.4 Handle case when P2P node is not initialized (return 503)
- [x] 1.5 Register the new route in the Express app

## 2. Server - Update Auth Response

- [x] 2.1 Remove `p2p` field from GitHub OAuth callback response in `apps/server/src/routes/auth.ts`
- [x] 2.2 Update response type to exclude `p2p` property
- [x] 2.3 Remove unused P2P imports from auth.ts if no longer needed

## 3. Client - Update Auth Interface

- [x] 3.1 Remove `p2p` field from `Auth` interface in `apps/client/src/stores/auth.ts`
- [x] 3.2 Remove `P2PConfig` import from auth.ts if no longer needed

## 4. Client - Create P2P Config Query Atom

- [x] 4.1 Import `atomWithQuery` from `jotai-tanstack-query` in `apps/client/src/p2p/store.ts`
- [x] 4.2 Create `p2pConfigQueryAtom` using `atomWithQuery`
- [x] 4.3 Configure query to fetch from `/api/p2p/config` with auth header
- [x] 4.4 Set `enabled` option based on auth state (only fetch when logged in)
- [x] 4.5 Update `p2pConfigAtom` to read from query atom data

## 5. Client - Update P2P Store Dependencies

- [x] 5.1 Verify `p2pConfigAtom` still returns `P2PConfig | null` interface
- [x] 5.2 Ensure `p2pConnectionEffect` still works with new config source
- [x] 5.3 Test that P2P auto-connect still triggers correctly after login

## 6. Verification

- [x] 6.1 Type-check both server and client: `npx tsc --noEmit`
- [x] 6.2 Lint check: `npx biome check .`
- [x] 6.3 Verify login flow works and P2P config is fetched separately
- [x] 6.4 Verify P2P connection establishes correctly with new config source
