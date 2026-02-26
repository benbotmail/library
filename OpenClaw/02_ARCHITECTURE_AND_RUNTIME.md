# 02 — Architecture and Runtime

Primary source: `openclaw-src/docs/concepts/architecture.md`

## Shape
- Single gateway process owns channel sessions + routing.
- Clients connect over gateway APIs/WS.
- Nodes connect with node role for device capabilities.
- Events/requests flow through typed gateway protocol.

## Invariants
- One gateway instance coordinates active host channel state.
- Side-effecting APIs rely on idempotency/correlation semantics.
- Session keys partition direct/group/thread contexts.

## Practical mental model
Channels -> Gateway -> Agent + Tools -> Outbound channel delivery.

## Deep reads
- `openclaw-src/docs/gateway/protocol.md`
- `openclaw-src/docs/concepts/session.md`
- `openclaw-src/docs/concepts/streaming.md`
