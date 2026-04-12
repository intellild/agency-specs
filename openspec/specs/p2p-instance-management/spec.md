## ADDED Requirements

### Requirement: Libp2p instance module-level storage
The system SHALL store the libp2p node instance in a module-level variable outside of jotai state management.

#### Scenario: Instance storage
- **WHEN** a libp2p node is created
- **THEN** it SHALL be stored in a module-level variable via `setLibp2pNode()`

#### Scenario: Instance retrieval
- **WHEN** code needs to access the libp2p node
- **THEN** it SHALL retrieve it via `getLibp2pNode()`

### Requirement: Server connection module-level storage
The system SHALL store the server connection instance in a module-level variable outside of jotai state management.

#### Scenario: Connection storage
- **WHEN** a server connection is established
- **THEN** it SHALL be stored in a module-level variable via `setServerConnection()`

#### Scenario: Connection retrieval
- **WHEN** code needs to access the server connection
- **THEN** it SHALL retrieve it via `getServerConnection()`

### Requirement: Instance cleanup on disconnect
The system SHALL properly clean up module-level instance references when disconnecting.

#### Scenario: Disconnect cleanup
- **WHEN** `disconnectP2PAtom` is invoked
- **THEN** the libp2p node SHALL be stopped
- **AND** both `setLibp2pNode(null)` and `setServerConnection(null)` SHALL be called

### Requirement: Instance cleanup on reset
The system SHALL provide a way to reset module-level instance references without stopping.

#### Scenario: Reset without stopping
- **WHEN** `resetP2PAtom` is invoked
- **THEN** both instance references SHALL be set to null via accessor functions
