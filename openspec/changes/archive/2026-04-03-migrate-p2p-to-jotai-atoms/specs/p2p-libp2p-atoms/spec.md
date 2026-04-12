## ADDED Requirements

### Requirement: Libp2p node instance management
The system SHALL manage the libp2p node instance through a jotai atom that can be null (not initialized) or contain an active Libp2p instance.

#### Scenario: Node initialization
- **WHEN** the libp2p node atom is set with a valid Libp2p instance
- **THEN** the atom SHALL hold the reference to that instance

#### Scenario: Node cleanup
- **WHEN** the libp2p node atom is set to null
- **THEN** any existing libp2p instance SHALL be properly stopped and cleaned up

### Requirement: Connection state atom
The system SHALL track the current P2P connection state through a jotai atom with the following states: 'idle', 'relay-connecting', 'relay-connected', 'signal-exchanging', 'webrtc-connecting', 'connected', 'disconnected', 'error'.

#### Scenario: State transitions
- **WHEN** the connect function is called
- **THEN** the connection state atom SHALL transition from 'idle' to 'relay-connecting'

#### Scenario: Error state
- **WHEN** a connection error occurs
- **THEN** the connection state atom SHALL be set to 'error' and the error message SHALL be stored in the error atom

### Requirement: Auto-connect on login
The system SHALL automatically initiate P2P connection when a valid auth token and P2P config are available.

#### Scenario: Successful auto-connect
- **GIVEN** the user is authenticated with valid P2P config
- **WHEN** the auth state contains P2P configuration
- **THEN** the system SHALL automatically start the P2P connection process

#### Scenario: Missing config
- **GIVEN** the user is authenticated but P2P config is null
- **WHEN** the auth state changes
- **THEN** the system SHALL NOT attempt to connect

### Requirement: Reconnection via atom effects
The system SHALL implement reconnection logic using jotai atom effects with exponential backoff.

#### Scenario: Reconnection after disconnect
- **GIVEN** the connection was previously established
- **WHEN** the connection state becomes 'disconnected'
- **THEN** the reconnection effect SHALL schedule a reconnect attempt with exponential backoff

#### Scenario: Max reconnection attempts
- **GIVEN** reconnection has been attempted 5 times
- **WHEN** the max attempts are reached
- **THEN** the reconnection effect SHALL stop and set the state to 'error'

### Requirement: P2P config from auth
The system SHALL derive P2P configuration from the auth atom and store it in a dedicated P2P config atom.

#### Scenario: Config extraction
- **WHEN** the auth atom contains P2P configuration
- **THEN** the P2P config atom SHALL be updated with the server peer ID, relay addresses, and ICE servers

### Requirement: Connection info atom
The system SHALL provide a computed atom that exposes connection information including peer ID, relay connection status, and direct connection status.

#### Scenario: Info updates
- **WHEN** the connection state changes
- **THEN** the connection info atom SHALL reflect the current peer ID and connection types

### Requirement: Cleanup on logout
The system SHALL automatically disconnect and clean up P2P resources when the user logs out.

#### Scenario: Logout cleanup
- **WHEN** the auth atom becomes null (user logs out)
- **THEN** the libp2p node SHALL be stopped and all P2P state SHALL be reset to initial values
