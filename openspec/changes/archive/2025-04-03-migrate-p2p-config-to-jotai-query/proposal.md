## Why

Currently, `p2pConfig` is embedded in the auth response and stored as part of the `Auth` type. This creates tight coupling between authentication and P2P configuration, making it harder to refresh P2P config independently or lazy-load it only when needed. Separating concerns by using a dedicated API endpoint with jotai-tanstack-query enables better caching, automatic refetching, and cleaner architecture.

## What Changes

- **Server**: Create new `GET /api/p2p/config` endpoint to expose P2P configuration (serverPeerId, relayAddresses, iceServers)
- **Server**: Remove `p2p` field from auth callback response in `/api/auth/github/callback` **BREAKING**
- **Client**: Remove `p2p` field from `Auth` interface in `stores/auth.ts` **BREAKING**
- **Client**: Create a new jotai-tanstack-query atom to fetch P2P config from the new endpoint
- **Client**: Update `p2p/store.ts` to use the query atom instead of deriving from auth
- **Client**: Ensure P2P config is only fetched after user is logged in (conditional query enabled based on auth state)

## Capabilities

### New Capabilities
- `p2p-config-api`: Server endpoint to provide P2P configuration separately from authentication
- `p2p-config-query`: Client-side jotai-tanstack-query integration for fetching and caching P2P config

### Modified Capabilities
- `auth`: Remove P2P config from authentication response and Auth type (implementation change only, auth behavior unchanged)

## Impact

- **API**: New endpoint `GET /api/p2p/config` (requires authentication)
- **Auth Response**: `p2p` field removed from `/api/auth/github/callback` response
- **Client Stores**: `Auth` interface simplified, P2P config moved to separate query atom
- **Dependencies**: Uses existing `jotai-tanstack-query` package (already installed)
