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
The system SHALL expose P2P node information through a utility function for use in authentication responses.

#### Scenario: Auth response includes P2P config
- **WHEN** a user successfully authenticates via GitHub OAuth
- **AND** the P2P node is initialized
- **THEN** the auth response includes `p2p.serverPeerId`, `p2p.relayAddresses`, and `p2p.iceServers`

#### Scenario: Auth response without P2P when unavailable
- **WHEN** a user successfully authenticates
- **AND** the P2P node is not initialized
- **THEN** the auth response includes `p2p: null`
