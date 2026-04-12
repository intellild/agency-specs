## Context

Currently, the P2P state management in `store.ts` includes:
- `enabled` field in `P2PConnectionState` - tracks if browser supports libp2p
- `p2pAutoConnectAtom` - localStorage-backed atom for auto-connect preference
- `p2pAutoConnectEffect` - effect that auto-connects when conditions are met

The `enabled` field is set by `p2pInitEffect` based on `isLibp2pSupported()` result. However, this is redundant since `isLibp2pSupported()` can be called anywhere at runtime.

The auto-connect toggle adds UI complexity that may not be necessary - users typically want P2P to connect when available.

## Goals / Non-Goals

**Goals:**
- Simplify `P2PConnectionState` by removing the `enabled` field
- Remove `p2pAutoConnectAtom` and related auto-connect preference
- Remove auto-connect toggle from Dashboard UI
- Keep the disconnect-on-logout behavior in the effect
- Update `canConnect` logic to not depend on `enabled`

**Non-Goals:**
- Changing the actual P2P connection behavior (just removing the toggle)
- Removing reconnection logic
- Changing the connection state machine

## Decisions

1. **Remove `enabled` field entirely**
   - Rationale: `isLibp2pSupported()` can be called directly when needed
   - The field was only used in `canConnect` calculation and UI enablement

2. **Remove `p2pAutoConnectAtom`**
   - Rationale: Auto-connect should be the default behavior
   - Users can still manually disconnect if needed
   - Simplifies the mental model: authenticated → auto-connect

3. **Keep `p2pAutoConnectEffect` but simplify**
   - Remove the `enabled && autoConnect` checks
   - Keep the disconnect-on-logout behavior
   - Rename to `p2pConnectionEffect` to reflect its purpose

4. **Update `canConnect` in `p2pStatusAtom`**
   - Remove `enabled` from the check
   - Now depends only on: config, auth token, not already connected/connecting

## Risks / Trade-offs

- **[Risk]** Users lose ability to disable auto-connect
  - **Mitigation**: Users can manually disconnect after login if needed
  - This is acceptable as the common case is wanting P2P connected

- **[Risk]** Code that read `enabled` field will break
  - **Mitigation**: This is a breaking change documented in proposal
  - All references in the codebase will be updated in this change

## Migration Plan

1. Update `store.ts` - Remove `enabled` field and `p2pAutoConnectAtom`
2. Update `hooks.ts` - Remove autoConnect from hook return value
3. Update `index.ts` - Remove exports
4. Update `dashboard.tsx` - Remove auto-connect toggle button
5. Test that P2P connection still works normally
