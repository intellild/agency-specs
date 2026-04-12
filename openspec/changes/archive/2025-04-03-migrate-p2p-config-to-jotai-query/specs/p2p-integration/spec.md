## MODIFIED Requirements

### Requirement: P2P info exposure
The system SHALL expose P2P node information through a dedicated API endpoint instead of embedding it in authentication responses.

#### Scenario: Auth response includes P2P config
- **REMOVED**: Auth response no longer includes P2P configuration
- **Migration**: Use `GET /api/p2p/config` endpoint

#### Scenario: P2P config available via dedicated endpoint
- **WHEN** a user is authenticated
- **AND** makes a GET request to `/api/p2p/config`
- **THEN** the response includes `serverPeerId`, `relayAddresses`, and `iceServers`

#### Scenario: Auth response without P2P when unavailable
- **REMOVED**: This scenario no longer applies as P2P config is not in auth response
- **Migration**: The `/api/p2p/config` endpoint returns 503 when P2P is unavailable
