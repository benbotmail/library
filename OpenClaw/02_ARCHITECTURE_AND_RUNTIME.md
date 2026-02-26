# 02 — Architecture and Runtime

## Gateway-centered architecture
Key source: `openclaw-src/docs/concepts/architecture.md`

- Single long-lived Gateway owns channel/provider connections.
- Clients (CLI, app, web UI, automations) connect via WebSocket.
- Nodes (macOS/iOS/Android/headless) connect via WS with `role: node`.
- Gateway also serves canvas host paths (`/__openclaw__/canvas/`, `/__openclaw__/a2ui/`).

## Protocol shape (high level)
- First frame must be `connect`.
- Request/response frame model with typed payloads.
- Server-push events for streaming + state updates.
- Channel streaming is preview/message-based and layered separately from block reply chunking.
- Token/password auth can gate connections.
- Idempotency keys expected for side-effecting operations.

## Invariants
- One gateway instance controls a host’s active channel sessions.
- Handshake enforcement is strict.
- Events are not replay logs; clients must recover on gaps.

## Runtime topology
Chat channels -> Gateway -> Agent runtime + tools + clients/nodes.

## Where to read deeper
- Protocol: `openclaw-src/docs/gateway/protocol.md` (if present in your checkout)
- Concepts: `openclaw-src/docs/concepts/*`
- Web/control plane docs: `openclaw-src/docs/web/*`
