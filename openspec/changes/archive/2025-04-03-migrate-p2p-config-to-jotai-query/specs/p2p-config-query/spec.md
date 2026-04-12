## ADDED Requirements

### Requirement: P2P config query atom
The system SHALL provide a jotai-tanstack-query atom for fetching P2P configuration from the server.

#### Scenario: Fetch P2P config when authenticated
- **WHEN** the user is logged in (authAtom has accessToken)
- **AND** the `p2pConfigQueryAtom` is subscribed
- **THEN** the system makes a GET request to `/api/p2p/config`
- **AND** includes the JWT access token in the Authorization header
- **AND** returns the P2P configuration data

#### Scenario: Skip fetch when not authenticated
- **WHEN** the user is not logged in (authAtom is null)
- **AND** the `p2pConfigQueryAtom` is subscribed
- **THEN** the query remains in 'pending' state
- **AND** no network request is made

#### Scenario: Handle fetch error
- **WHEN** the P2P config fetch fails (network error or 5xx response)
- **THEN** the query enters 'error' state
- **AND** the error is available for error handling

### Requirement: P2P config atom interface
The system SHALL maintain the `p2pConfigAtom` interface while changing its implementation to use the query atom.

#### Scenario: Read P2P config from query
- **WHEN** `p2pConfigAtom` is read
- **THEN** it returns the data from `p2pConfigQueryAtom` if available
- **AND** returns `null` if the query has no data

#### Scenario: P2P config availability for connection
- **WHEN** P2P connection logic checks for config availability
- **THEN** it uses `p2pConfigAtom` which sources from the query
- **AND** behaves identically to the previous implementation
