## MODIFIED Requirements

### Requirement: GitHub OAuth callback endpoint
The system SHALL provide a POST endpoint `/auth/github/callback` that exchanges GitHub authorization code for access tokens.

#### Scenario: Successful GitHub OAuth
- **WHEN** a POST request is made to `/auth/github/callback` with a valid `{ code }` body
- **THEN** the system exchanges the code for a GitHub access token
- **AND** fetches the authenticated GitHub user info
- **AND** generates a new JWT access token (RS256, 30 days expiration)
- **AND** generates a new JWT refresh token (HS256)
- **AND** returns `{ accessToken, refreshToken, githubToken, userId, username }`
- **AND** the response does NOT include a `p2p` field

## REMOVED Requirements

### Requirement: P2P config in auth response
**Reason**: P2P configuration is now fetched separately via the `/api/p2p/config` endpoint to decouple auth from P2P concerns.

**Migration**: Clients should fetch P2P config separately using the new `GET /api/p2p/config` endpoint after successful authentication.

#### Scenario: Auth response includes P2P config
- **REMOVED**: Auth response no longer includes `p2p` field
- **Migration**: Use `GET /api/p2p/config` endpoint instead
