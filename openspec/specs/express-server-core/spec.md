## ADDED Requirements

### Requirement: Express application initialization
The system SHALL create an Express application instance with JSON body parsing and CORS support.

#### Scenario: Server starts successfully
- **WHEN** the server application starts
- **THEN** an Express app instance is created
- **AND** JSON body parser middleware is registered
- **AND** CORS middleware is configured to allow localhost origins

### Requirement: Route registration
The system SHALL register all API routes on the Express application.

#### Scenario: Routes are accessible
- **WHEN** the server receives HTTP requests to registered paths
- **THEN** the appropriate route handlers are invoked
- **AND** routes include `/auth/*`, `/api/*`, and `/`

### Requirement: Server startup and shutdown
The system SHALL start the HTTP server on configured host and port, and gracefully handle shutdown.

#### Scenario: Server listens on configured port
- **WHEN** the server starts with HOST=localhost and PORT=3000
- **THEN** the server listens on http://localhost:3000

#### Scenario: Graceful shutdown
- **WHEN** the server receives SIGTERM signal
- **THEN** P2P node is stopped before process exits
