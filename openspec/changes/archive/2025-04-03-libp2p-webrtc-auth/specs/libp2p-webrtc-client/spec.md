## ADDED Requirements

### Requirement: Client libp2p initialization
The client SHALL create a libp2p node in the browser environment after successful login.

#### Scenario: Initialize after login
- **WHEN** the user successfully logs in
- **AND** the login response contains P2P configuration
- **THEN** the client SHALL initialize a libp2p node

#### Scenario: Browser compatibility check
- **WHEN** initializing the libp2p node
- **AND** the browser does not support WebRTC
- **THEN** the client SHALL skip P2P initialization
- **AND** the application SHALL continue using HTTP APIs

### Requirement: Circuit Relay connection
The client SHALL connect to the server's Circuit Relay to facilitate WebRTC signaling.

#### Scenario: Establish relay circuit
- **WHEN** the client has initialized its libp2p node
- **THEN** the client SHALL dial the server's relay address
- **AND** request a relay reservation
- **AND** include the accessToken in the reservation request

#### Scenario: Relay with authentication
- **WHEN** requesting a relay reservation
- **THEN** the client SHALL provide a valid accessToken
- **AND** the reservation SHALL only be granted upon successful authentication

### Requirement: WebRTC connection through relay
The client SHALL use the Circuit Relay to exchange WebRTC signaling and establish a direct connection.

#### Scenario: Exchange SDP through relay
- **GIVEN** the client has an active relay circuit to the server
- **WHEN** initiating WebRTC connection
- **THEN** the client SHALL send SDP offer through the relay circuit
- **AND** receive SDP answer through the relay circuit

#### Scenario: Exchange ICE candidates
- **GIVEN** an active relay circuit
- **WHEN** ICE candidates are generated
- **THEN** candidates SHALL be exchanged through the relay circuit
- **UNTIL** the WebRTC connection is established

#### Scenario: Direct WebRTC connection
- **GIVEN** signaling is complete via relay
- **WHEN** the WebRTC connection state becomes "connected"
- **THEN** the client SHALL switch to the direct WebRTC connection
- **AND** the relay circuit MAY be closed

### Requirement: Connection state management
The client SHALL track and expose the P2P connection state.

#### Scenario: Connection established
- **WHEN** the WebRTC connection to server is established
- **THEN** the client SHALL update connection state to "connected"
- **AND** the application SHALL be able to send/receive data via P2P

#### Scenario: Connection lost
- **WHEN** the P2P connection is lost
- **THEN** the client SHALL update connection state to "disconnected"
- **AND** the client SHALL attempt reconnection with exponential backoff
- **AND** fallback to Circuit Relay if WebRTC fails
