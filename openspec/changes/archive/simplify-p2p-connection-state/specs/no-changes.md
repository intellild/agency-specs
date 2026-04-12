## No Spec Changes

This change is an implementation simplification only. No spec-level requirements are being added, modified, or removed.

The changes are purely internal code refactoring:
- Removing redundant `enabled` field from state
- Removing auto-connect preference atom
- Simplifying the P2P state management

All existing P2P behavior specifications remain unchanged.
