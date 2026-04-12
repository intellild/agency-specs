## ADDED Requirements

### Requirement: GitHub OAuth callback endpoint
The system SHALL provide a POST endpoint `/auth/github/callback` that exchanges GitHub authorization code for access tokens.

#### Scenario: Successful GitHub OAuth
- **WHEN** a POST request is made to `/auth/github/callback` with a valid `{ code }` body
- **THEN** the system exchanges the code for a GitHub access token
- **AND** fetches the authenticated GitHub user info
- **AND** generates a new JWT access token (RS256, 30 days expiration)
- **AND** generates a new JWT refresh token (HS256)
- **AND** returns `{ accessToken, refreshToken, githubToken, userId, username }`
- **AND** the response does NOT include a `p2p` field

#### Scenario: Missing authorization code
- **WHEN** a POST request is made without a code
- **THEN** the system redirects to `${FRONTEND_URL}/login?error=missing_code`

#### Scenario: Invalid GitHub code
- **WHEN** a POST request is made with an invalid/expired code
- **THEN** the system responds with error status
- **AND** the error is logged

### Requirement: Token refresh endpoint
The system SHALL provide a POST endpoint `/auth/refresh` that accepts a refresh token and returns new tokens.

#### Scenario: Successful token refresh
- **WHEN** a POST request is made to `/auth/refresh` with a valid `{ refresh_token }` body
- **THEN** the system verifies the refresh token using HS256 and root secret
- **AND** checks that the token type is 'refresh'
- **AND** generates new access and refresh tokens (token rotation)
- **AND** returns `{ access_token, refresh_token }`

#### Scenario: Missing refresh token
- **WHEN** a POST request is made to `/auth/refresh` without a refresh_token
- **THEN** the system responds with 400 status
- **AND** the response body contains `{ error: 'Refresh token required' }`

#### Scenario: Invalid refresh token
- **WHEN** a POST request is made to `/auth/refresh` with an invalid or expired refresh token
- **THEN** the system responds with 403 status
- **AND** the response body contains `{ error: 'Invalid refresh token' }`

#### Scenario: Wrong token type for refresh
- **WHEN** a POST request is made to `/auth/refresh` with an access token instead of refresh token
- **THEN** the system responds with 403 status
- **AND** the response body contains `{ error: 'Invalid token type' }`
