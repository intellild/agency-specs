## ADDED Requirements

### Requirement: Access token validation on connection
The server SHALL validate the accessToken for every incoming P2P connection attempt.

#### Scenario: Valid token acceptance
- **WHEN** a client connects with a valid accessToken
- **THEN** the server SHALL accept the connection
- **AND** the server SHALL associate the connection with the authenticated user

#### Scenario: Invalid token rejection
- **WHEN** a client connects with an invalid or expired accessToken
- **THEN** the server SHALL reject the connection
- **AND** the server SHALL log the rejection event

#### Scenario: Missing token rejection
- **WHEN** a client connects without providing an accessToken
- **THEN** the server SHALL reject the connection immediately
- **AND** the server SHALL return an authentication error

### Requirement: Token extraction from connection
The server SHALL extract the accessToken from the libp2p connection handshake.

#### Scenario: Token in Noise handshake
- **WHEN** a client initiates a libp2p connection
- **AND** the client includes the accessToken in the Noise protocol handshake
- **THEN** the server SHALL extract and validate the token

#### Scenario: Token in protocol selection
- **WHEN** using protocol-based authentication
- **THEN** the accessToken SHALL be passed as part of the protocol negotiation

### Requirement: Connection lifecycle management
The server SHALL terminate P2P connections when the associated token expires.

#### Scenario: Token expiration during connection
- **GIVEN** an established P2P connection
- **WHEN** the associated accessToken expires
- **THEN** the server SHALL close the connection
- **AND** notify the client to re-authenticate

### Requirement: Rate limiting per user
The server SHALL limit the number of concurrent P2P connections per user.

#### Scenario: Connection limit enforcement
- **WHEN** a user attempts to establish a new P2P connection
- **AND** the user already has the maximum allowed connections
- **THEN** the server SHALL reject the new connection
- **AND** return a "too many connections" error
