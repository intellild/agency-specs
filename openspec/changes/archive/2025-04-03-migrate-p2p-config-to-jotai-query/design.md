## Context

Currently, the P2P configuration is tightly coupled with authentication:
- Server: `POST /api/auth/github/callback` returns `p2p` object containing `serverPeerId`, `relayAddresses`, and `iceServers`
- Client: `Auth` interface in `stores/auth.ts` includes `p2p?: P2PConfig | null`
- Client: `p2pConfigAtom` in `p2p/store.ts` derives from `authAtom` via `get(authAtom)?.p2p ?? null`

This coupling makes it difficult to:
1. Refresh P2P configuration independently of authentication
2. Lazy-load P2P config only when needed
3. Leverage TanStack Query's caching and refetching capabilities

## Goals / Non-Goals

**Goals:**
- Decouple P2P configuration from authentication flow
- Create a dedicated server endpoint for P2P configuration
- Use jotai-tanstack-query for client-side data fetching with automatic caching
- Ensure P2P config is only fetched when user is authenticated
- Maintain backward compatibility for all other auth-related functionality

**Non-Goals:**
- Changing P2P connection logic or libp2p implementation
- Modifying the P2PConfig type structure
- Adding new authentication methods
- Implementing P2P config refresh/interval polling (can be added later)

## Decisions

### 1. New API Endpoint: `GET /api/p2p/config`
**Rationale**: A dedicated endpoint follows REST principles and enables independent fetching.
- Returns `P2PConfig` object: `{ serverPeerId, relayAddresses, iceServers }`
- Requires authentication (JWT bearer token)
- Uses existing `getP2PNodeInfo()` and `getICEServers()` functions from `p2p/index.js`

### 2. Remove `p2p` from Auth Response
**Rationale**: Eliminates data duplication and enforces the new pattern.
- **Alternative considered**: Keep `p2p` in auth for backward compatibility - rejected to enforce clean separation

### 3. Use `atomWithQuery` from jotai-tanstack-query
**Rationale**: Provides built-in caching, error handling, and loading states.
- Query key: `['p2p', 'config']` 
- Query function: `GET /api/p2p/config` with auth header
- `enabled` option tied to auth state (only fetch when logged in)

### 4. Update `p2pConfigAtom` to Use Query Result
**Rationale**: Maintains existing interface while changing implementation.
- Change from derived atom to atom that reads from query atom
- Returns `P2PConfig | null` (same interface, different source)

## Risks / Trade-offs

**[Risk]** Breaking change for any external clients relying on `p2p` in auth response
→ **Mitigation**: This is an internal application; both server and client are in the same repo and will be updated together

**[Risk]** Additional network request on login (slight latency increase)
→ **Mitigation**: P2P connection already happens asynchronously; the config fetch can run in parallel with other post-login operations

**[Risk]** Query cache may have stale P2P config if server config changes
→ **Mitigation**: P2P config is relatively static (server peer ID, relay addresses); can add cache invalidation later if needed

## Migration Plan

1. **Server**: Add new `GET /api/p2p/config` endpoint in new file `routes/p2p.ts`
2. **Server**: Remove `p2p` from auth callback response in `routes/auth.ts`
3. **Client**: Remove `p2p` field from `Auth` interface in `stores/auth.ts`
4. **Client**: Create `p2pConfigQueryAtom` using `atomWithQuery` in `p2p/store.ts`
5. **Client**: Update `p2pConfigAtom` to read from query atom
6. **Test**: Verify login flow still works and P2P config is fetched correctly

## Open Questions

None - implementation approach is clear.
