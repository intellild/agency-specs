## ADDED Requirements

### Requirement: P2P node initialization
The system SHALL initialize the P2P node after the Express server starts listening.

#### Scenario: Server startup with P2P
- **WHEN** the Express server starts and begins listening
- **THEN** the P2P node is initialized asynchronously
- **AND** a success message is logged
- **AND** server continues to function even if P2P initialization fails

#### Scenario: P2P initialization failure
- **WHEN** the P2P node fails to initialize
- **THEN** the error is logged
- **AND** the HTTP server continues running
- **AND** no P2P configuration is returned in auth responses

### Requirement: P2P node shutdown
The system SHALL gracefully stop the P2P node when the server shuts down.

#### Scenario: Graceful shutdown
- **WHEN** the server process receives SIGTERM or SIGINT
- **THEN** the P2P node is stopped before the process exits

### Requirement: P2P info exposure
The system SHALL expose P2P node information through a dedicated API endpoint instead of embedding it in authentication responses.

#### Scenario: Auth response includes P2P config
- **REMOVED**: Auth response no longer includes P2P configuration
- **Migration**: Use `GET /api/p2p/config` endpoint

#### Scenario: P2P config available via dedicated endpoint
- **WHEN** a user is authenticated
- **AND** makes a GET request to `/api/p2p/config`
- **THEN** the response includes `serverPeerId`, `relayAddresses`, and `iceServers`

#### Scenario: P2P unavailable response
- **WHEN** a user requests P2P config
- **AND** the P2P node is not initialized
- **THEN** the `/api/p2p/config` endpoint returns 503
