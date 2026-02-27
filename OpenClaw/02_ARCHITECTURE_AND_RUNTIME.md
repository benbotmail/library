# 02 — Architecture and Runtime

Primary refs:
- `openclaw-src/docs/concepts/architecture.md`
- `openclaw-src/docs/gateway/protocol.md`
- `openclaw-src/docs/concepts/session.md`

## Shape
- One gateway process owns channel adapters and session routing.
- Clients/nodes interact via gateway APIs/websocket protocol.
- Tools execute under policy controls (security mode, workspace constraints, sandboxing).

## Invariants
- Deterministic routing by channel/thread/session key.
- Group and DM policy checks are applied before turn execution.
- Runtime is fail-closed for missing provider config in group contexts (`allowlist` fallback behavior).

## Practical model
Channels → Gateway router/policies → Agent + tools → Channel-specific outbound delivery.
