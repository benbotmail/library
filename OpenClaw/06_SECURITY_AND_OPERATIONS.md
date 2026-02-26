# 06 — Security and Operations

## Security posture summary
OpenClaw treats inbound chat surfaces as untrusted input. Pairing, allowlists, auth tokens/passwords, and sandbox boundaries are core controls.

## Critical docs
- `openclaw-src/docs/gateway/security/index.md`
- `openclaw-src/docs/channels/troubleshooting.md`
- `openclaw-src/docs/gateway/*` (ops runbooks)
- CLI security surface: `openclaw-src/docs/cli/security.md`

## Current policy defaults/operators should know
- Heartbeat direct/DM delivery policy is explicit via `agents.defaults.heartbeat.directPolicy` (`allow` or `block`).
- Keep channel authorization fail-closed in shared/group contexts (use explicit allowlists).
- Treat reaction/interaction ingress as privileged input and keep policy checks aligned with normal message authorization.

## Operational hygiene checklist
- Use `openclaw doctor` for drift and common misconfigurations.
- Use `openclaw status` / `openclaw gateway status` before invasive changes.
- Prefer `--json` for machine-checkable diagnostics.
- Keep gateway bind/auth configuration explicit.
- Review remote exposure docs before enabling remote access.

## Remote access caution
Prefer tailnet/private links or SSH tunnels. Avoid exposing unauthenticated/public endpoints.

## Incident triage order (fast path)
1. Is gateway alive?
2. Is auth/pairing valid?
3. Is channel connected?
4. Are sessions/routing policy causing behavior?
5. What do logs show?
