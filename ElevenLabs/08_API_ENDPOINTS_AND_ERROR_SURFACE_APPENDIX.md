# 08 — API Endpoints and Error Surface Appendix

This appendix captures the **canonical endpoint surface** needed for the architectures in this pack.

Scope: conversation sessions + Scribe realtime STT token flow + self-hosted orchestrator.
Source basis: `open-source/elevenlabs/packages/client/README.md` + source code (current monorepo state at v1.20.0, commit `fc2380cf`).

## 1) Vendor endpoints (server-to-ElevenLabs)

### A) Conversation signed URL (WebSocket flow)
- Method: `GET`
- Endpoint:
  - `https://api.elevenlabs.io/v1/convai/conversation/get-signed-url?agent_id=<AGENT_ID>`
- Required header:
  - `xi-api-key: <ELEVENLABS_API_KEY>`
- Expected response shape:
  - `{ "signed_url": "..." }`

### B) Conversation token (WebRTC flow)
- Method: `GET`
- Endpoint:
  - `https://api.elevenlabs.io/v1/convai/conversation/token?agent_id=<AGENT_ID>`
- Required header:
  - `xi-api-key: <ELEVENLABS_API_KEY>`
- Expected response shape:
  - `{ "token": "..." }`

### C) Scribe single-use token
- Method: `POST`
- Endpoint:
  - `https://api.elevenlabs.io/v1/single-use-token/realtime_scribe`
- Required header:
  - `xi-api-key: <ELEVENLABS_API_KEY>`
- Expected response shape:
  - `{ "token": "..." }`

### D) Overall conversation feedback
- Method: `POST`
- Endpoint:
  - `https://api.elevenlabs.io/v1/convai/conversations/{conversation_id}/feedback`
- Required header:
  - `xi-api-key: <ELEVENLABS_API_KEY>` (or client-side token for per-message feedback via SDK)

### E) Self-hosted orchestrator WebSocket (experimental)
- Protocol: WebSocket (`wss://`)
- URL: customer-defined (e.g., `wss://<your-host>/sagemaker/convai/conversation`)
- No subprotocol negotiation (unlike cloud WebSocket which uses `convai` protocol)
- On open, client sends `enclave_setup_config` event with agent config, tools, webhooks
- No `source`/`version` query params appended (unlike cloud sessions)
- Post-call webhooks: orchestrator calls your endpoints with transcript/audio after session ends

---

## 2) App-facing endpoints (your backend)

Recommended boundary endpoints so clients never receive permanent API keys:

- `GET /signed-url` — Returns plaintext signed URL
- `GET /conversation-token` — Returns WebRTC token
- `GET /scribe-token` — Returns realtime Scribe token

Hard requirements:
- Authenticate caller before minting
- Apply rate limits per user/session
- Redact tokens from logs

---

## 3) Minimal response/error contract for your backend

### Success envelope
```json
{
  "ok": true,
  "token": "...",
  "expires_at": 1740657600
}
```

### Error envelope
```json
{
  "ok": false,
  "code": "UPSTREAM_AUTH_FAILED",
  "message": "Failed to mint ElevenLabs token",
  "retryable": true
}
```

Error-code starter set: `UNAUTHORIZED`, `FORBIDDEN`, `RATE_LIMITED`, `UPSTREAM_AUTH_FAILED`, `UPSTREAM_RATE_LIMITED`, `UPSTREAM_UNAVAILABLE`, `BAD_REQUEST`

---

## 4) Runtime error events (Scribe)

Map these to actionable recovery:

| Event | Action |
|---|---|
| `AUTH_ERROR` | Refresh token + reconnect |
| `ERROR` | Classify (retryable vs fatal), backoff retry if retryable |
| `QUOTA_EXCEEDED` | Surface billing/limit state |
| `COMMIT_THROTTLED` | Reduce commit frequency |
| `TRANSCRIBER_ERROR` | Surface as server-side failure |
| `UNACCEPTED_TERMS` | Prompt terms acceptance |
| `RATE_LIMITED` | Backoff and retry |
| `INPUT_ERROR` | Validate input format |
| `INVALID_REQUEST` | Connection parameters rejected server-side — fix `Scribe.connect()` options and reconnect (v1.20.0) |
| `QUEUE_OVERFLOW` | Reduce audio chunk rate |
| `RESOURCE_EXHAUSTED` | Retry with backoff |
| `SESSION_TIME_LIMIT_EXCEEDED` | Start new session |
| `CHUNK_SIZE_EXCEEDED` | Reduce chunk size |
| `INSUFFICIENT_AUDIO_ACTIVITY` | Check microphone input |
| `CLOSE` | Reconnect policy with jitter and max attempts |

> **v1.15.1 robustness:** Generic local Scribe errors now use the same typed error payload as server errors, and malformed error messages (missing `error_event` payload) are handled defensively instead of crashing the consumer.
>
> **v1.20.0 events:** the SDK now also dispatches `final_transcript`, `final_transcript_with_timestamps`, `committed_transcript_entities` (with `entityDetection`), and `invalid_request` as first-class `RealtimeEvents`. See `03_SCRIBE_REALTIME_STT_PATTERNS.md`.

---

## 5) Conversation disconnection details

`onDisconnect` receives `DisconnectionDetails`:

```ts
type DisconnectionDetails =
  | { reason: "error"; message: string; context: DisconnectionContext; closeCode?: number; closeReason?: string }
  | { reason: "agent"; context?: DisconnectionContext; closeCode?: number; closeReason?: string }
  | { reason: "user" };
```

Use `reason` to drive smart retry: only auto-retry on `"error"`, prompt user on `"agent"`, and do nothing on `"user"`.

---

## 6) Endpoint drift check (doc-freshness guard)

On each docs refresh run:
1. Verify each endpoint string still exists in upstream docs/examples
2. Confirm method (`GET` vs `POST`) unchanged
3. Confirm expected key fields (`signed_url`, `token`) unchanged
4. Update this file if any mismatch is found

If drift is detected, mark this appendix with a **BREAKING CHANGE NOTE** at top before publishing.
