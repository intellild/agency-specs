## ADDED Requirements

### Requirement: P2P config endpoint
The system SHALL provide a GET endpoint `/p2p/config` that returns P2P configuration for authenticated users.

#### Scenario: Successful P2P config retrieval
- **WHEN** a GET request is made to `/p2p/config` with a valid JWT access token
- **THEN** the system validates the authentication token
- **AND** returns `{ serverPeerId, relayAddresses, iceServers }`

#### Scenario: Unauthenticated request
- **WHEN** a GET request is made to `/p2p/config` without a valid JWT token
- **THEN** the system responds with 401 status
- **AND** the response body contains `{ error: 'Unauthorized' }`

#### Scenario: P2P node not initialized
- **WHEN** a GET request is made to `/p2p/config` with valid authentication
- **AND** the P2P node is not initialized
- **THEN** the system responds with 503 status
- **AND** the response body contains `{ error: 'P2P service unavailable' }`
