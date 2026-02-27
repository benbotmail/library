# Migration Note: After `agents-cli` Removal

At upstream commit `f61e7282529a92f7f3332cf1cb3d8fe2fe480df4`, `packages/agents-cli` and related tests were removed.

## Practical impact

- Any internal docs referencing `agents` CLI commands in this monorepo are stale.
- Release validation and package workflows now revolve around SDK packages only.

## Immediate migration actions

1. Remove CLI-specific instructions from internal runbooks.
2. Recenter onboarding around package-level integration (`client`, `react`, `react-native`, widgets, `types`).
3. Keep operational docs “current-state” (how to implement now) rather than changelog narratives.
