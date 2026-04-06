## ADDED Requirements

### Requirement: P2P configuration in login response
The login endpoint SHALL return libp2p connection configuration upon successful authentication.

#### Scenario: Login with P2P support
- **WHEN** a user successfully logs in
- **THEN** the login response SHALL include P2P configuration
- **AND** the configuration SHALL contain the server's libp2p PeerId
- **AND** the configuration SHALL contain the Circuit Relay multiaddresses
- **AND** the configuration SHALL contain recommended ICE servers

#### Scenario: P2P configuration structure
- **WHEN** the login response includes P2P configuration
- **THEN** the configuration SHALL have the following structure:
  ```json
  {
    "p2p": {
      "serverPeerId": "<libp2p-peer-id>",
      "relayAddresses": [
        "/dns4/example.com/tcp/443/wss/p2p/<relay-peer-id>/p2p-circuit"
      ],
      "iceServers": [
        { "urls": "stun:stun.example.com:3478" }
      ]
    }
  }
  ```

### Requirement: P2P permission check
The login endpoint SHALL include P2P connection permission in the response.

#### Scenario: User with P2P permission
- **WHEN** a user with P2P privileges logs in
- **THEN** the P2P configuration SHALL be included in the response

#### Scenario: User without P2P permission
- **WHEN** a user without P2P privileges logs in
- **THEN** the P2P configuration field SHALL be null or omitted
- **AND** the client SHALL not attempt P2P connection
