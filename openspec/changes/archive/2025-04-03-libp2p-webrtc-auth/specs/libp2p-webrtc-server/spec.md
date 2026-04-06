## ADDED Requirements

### Requirement: Server libp2p node initialization
The server SHALL create a libp2p node on startup with WebRTC transport enabled.

#### Scenario: Successful node startup
- **WHEN** the server starts
- **THEN** a libp2p node SHALL be initialized with WebRTC transport
- **AND** the node SHALL listen on a configured port

#### Scenario: WebRTC transport configuration
- **WHEN** configuring the libp2p node
- **THEN** the node SHALL use `@libp2p/webrtc` transport
- **AND** the transport SHALL be configured with the server's public address

### Requirement: Connection handling
The server SHALL handle incoming WebRTC connections through the libp2p node.

#### Scenario: Accept valid connection
- **WHEN** a client initiates a WebRTC connection
- **AND** the connection passes authentication
- **THEN** the server SHALL accept and maintain the connection

#### Scenario: Reject unauthenticated connection
- **WHEN** a client initiates a WebRTC connection
- **AND** the connection fails authentication
- **THEN** the server SHALL reject and close the connection

### Requirement: Node identification
The server SHALL expose its libp2p node ID for client connections.

#### Scenario: Node info endpoint
- **WHEN** a client requests node information
- **THEN** the server SHALL return its libp2p PeerId
- **AND** the server SHALL return the WebRTC signaling endpoint URL
