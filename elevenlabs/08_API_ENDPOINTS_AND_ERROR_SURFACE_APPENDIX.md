# 08 — API Endpoints and Error Surface Appendix

This appendix captures the **canonical endpoint surface** needed for the architectures in this pack.

Scope: conversation sessions + Scribe realtime STT token flow.
Source basis: `open-source/elevenlabs/packages/client/README.md` (current monorepo state).

## 1) Vendor endpoints (server-to-ElevenLabs)

### A) Conversation signed URL (WebSocket flow)
- Method: `GET`
- Endpoint:
  - `https://api.elevenlabs.io/v1/convai/conversation/get-signed-url?agent_id=<AGENT_ID>`
- Required header:
  - `xi-api-key: <ELEVENLABS_API_KEY>`
- Expected response shape (minimum used by client):
  - `{ "signed_url": "..." }`

### B) Conversation token (WebRTC flow)
- Method: `GET`
- Endpoint:
  - `https://api.elevenlabs.io/v1/convai/conversation/token?agent_id=<AGENT_ID>`
- Required header:
  - `xi-api-key: <ELEVENLABS_API_KEY>`
- Expected response shape (minimum used by client):
  - `{ "token": "..." }`

### C) Scribe single-use token
- Method: `POST`
- Endpoint:
  - `https://api.elevenlabs.io/v1/single-use-token/realtime_scribe`
- Required header:
  - `xi-api-key: <ELEVENLABS_API_KEY>`
- Expected response shape (minimum used by client):
  - `{ "token": "..." }`

---

## 2) App-facing endpoints (your backend)

These are recommended boundary endpoints so clients never receive permanent API keys.

- `GET /signed-url`
  - Returns plaintext signed URL (or JSON wrapper if preferred)
- `GET /conversation-token`
  - Returns WebRTC token
- `GET /scribe-token`
  - Returns realtime Scribe token

Hard requirements:
- authenticate caller before minting
- apply rate limits per user/session
- redact tokens from logs

---

## 3) Minimal response/error contract for your backend

### Success envelope (recommended)
```json
{
  "ok": true,
  "token": "...",
  "expires_at": 1740657600
}
```

### Error envelope (recommended)
```json
{
  "ok": false,
  "code": "UPSTREAM_AUTH_FAILED",
  "message": "Failed to mint ElevenLabs token",
  "retryable": true
}
```

Error-code starter set:
- `UNAUTHORIZED`
- `FORBIDDEN`
- `RATE_LIMITED`
- `UPSTREAM_AUTH_FAILED`
- `UPSTREAM_RATE_LIMITED`
- `UPSTREAM_UNAVAILABLE`
- `BAD_REQUEST`

---

## 4) Runtime error events to map explicitly

When using `@elevenlabs/client` realtime flows, map these to actionable recovery:

- `AUTH_ERROR`
  - action: refresh token + reconnect
- `ERROR`
  - action: classify (retryable vs fatal), then backoff retry if retryable
- `QUOTA_EXCEEDED`
  - action: surface billing/limit state to operator and user path
- `CLOSE`
  - action: reconnect policy with jitter and max attempts

---

## 5) Endpoint drift check (doc-freshness guard)

On each docs refresh run:
1. verify each endpoint string above still exists in upstream docs/examples
2. confirm method (`GET` vs `POST`) unchanged
3. confirm expected key fields (`signed_url`, `token`) unchanged
4. update this file if any mismatch is found

If drift is detected, mark this appendix with a **BREAKING CHANGE NOTE** at top before publishing.