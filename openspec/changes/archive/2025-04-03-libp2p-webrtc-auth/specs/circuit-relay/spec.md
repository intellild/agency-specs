## ADDED Requirements

### Requirement: Circuit Relay service enablement
The server SHALL enable Circuit Relay v2 service on its libp2p node.

#### Scenario: Relay service startup
- **WHEN** the libp2p node starts
- **THEN** the Circuit Relay v2 service SHALL be enabled
- **AND** the server SHALL advertise its relay addresses

#### Scenario: Relay reservation
- **WHEN** a client requests a relay reservation
- **THEN** the server SHALL validate the client's accessToken
- **AND** upon successful validation, grant a relay reservation

### Requirement: Relay-based signaling for WebRTC
The Circuit Relay SHALL be used to exchange WebRTC signaling messages (SDP offer/answer, ICE candidates).

#### Scenario: SDP offer through relay
- **GIVEN** a client has established a relay circuit to the server
- **WHEN** the client sends a WebRTC SDP offer
- **THEN** the server SHALL receive the offer through the relay circuit
- **AND** the server SHALL respond with an SDP answer through the same circuit

#### Scenario: ICE candidate exchange
- **GIVEN** an active relay circuit between client and server
- **WHEN** either peer generates an ICE candidate
- **THEN** the candidate SHALL be sent through the relay circuit
- **AND** the receiving peer SHALL add the candidate to its RTCPeerConnection

#### Scenario: WebRTC handoff
- **GIVEN** WebRTC connection is established
- **WHEN** the direct WebRTC connection becomes active
- **THEN** the relay circuit MAY be closed to free resources
- **AND** communication continues over the direct WebRTC connection

### Requirement: Relay connection limits
The server SHALL enforce limits on relay reservations per user.

#### Scenario: Reservation limit enforcement
- **WHEN** a user requests a new relay reservation
- **AND** the user has reached the maximum allowed reservations
- **THEN** the server SHALL reject the reservation request
- **AND** return a "reservation limit exceeded" error

#### Scenario: Reservation expiration
- **GIVEN** an active relay reservation
- **WHEN** the reservation duration exceeds the configured limit
- **THEN** the server SHALL expire the reservation
- **AND** notify the client to re-establish if needed

### Requirement: Relay security
The server SHALL authenticate clients before granting relay reservations.

#### Scenario: Authenticated reservation
- **WHEN** a client requests a relay reservation
- **THEN** the server SHALL validate the client's accessToken
- **AND** only grant the reservation if the token is valid and not expired

#### Scenario: Reject unauthenticated relay use
- **WHEN** an unauthenticated client attempts to use the relay service
- **THEN** the server SHALL reject the reservation request
- **AND** log the rejection event
