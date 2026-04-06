## ADDED Requirements

### Requirement: JWT authentication middleware
The system SHALL provide an Express middleware that validates JWT access tokens from the Authorization header.

#### Scenario: Valid access token
- **WHEN** a request includes a valid Bearer token in the Authorization header
- **THEN** the middleware verifies the token using RS256 and the public key
- **AND** the decoded user info (sub, type) is attached to `req.user`
- **AND** the request proceeds to the next middleware

#### Scenario: Missing authorization header
- **WHEN** a request to a protected endpoint has no Authorization header
- **THEN** the middleware responds with 401 status
- **AND** the response body contains `{ error: 'Missing or invalid authorization header' }`

#### Scenario: Invalid token format
- **WHEN** a request has an Authorization header that does not start with 'Bearer '
- **THEN** the middleware responds with 401 status
- **AND** the response body contains `{ error: 'Missing or invalid authorization header' }`

#### Scenario: Expired token
- **WHEN** a request includes an expired JWT token
- **THEN** the middleware responds with 401 status
- **AND** the response body contains `{ error: 'Token expired' }`

#### Scenario: Invalid token signature
- **WHEN** a request includes a JWT with invalid signature
- **THEN** the middleware responds with 401 status
- **AND** the response body contains `{ error: 'Invalid token' }`

#### Scenario: Wrong token type
- **WHEN** a request includes a valid JWT but with type !== 'access'
- **THEN** the middleware responds with 401 status
- **AND** the response body contains `{ error: 'Invalid token type' }`

### Requirement: Type extension
The system SHALL extend Express Request type to include the `user` property.

#### Scenario: TypeScript compilation
- **WHEN** TypeScript compiles the server code
- **THEN** `req.user` is recognized as `{ sub: string; type: string }`
- **AND** no type errors occur when accessing `req.user.sub`
