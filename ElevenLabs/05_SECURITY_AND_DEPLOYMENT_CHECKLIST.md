# Security and Deployment Checklist

## Credential boundaries

- Keep ElevenLabs API key server-side only.
- Browser/mobile should receive short-lived artifacts (`signed_url` or `conversation token`) from your backend.

## Minimum backend endpoints

- `GET /signed-url` (WebSocket private sessions)
- `GET /conversation-token` (WebRTC private sessions)

Protect both with your auth middleware.

## Logging

Do log:
- connection status changes
- session IDs / conversation IDs
- tool invocation failures

Do not log:
- raw API keys
- unredacted PII transcripts without policy approval

## Rollout safety

- Use a canary group when changing connection type defaults.
- Add SLA monitors for connect latency, interruption rate, and transcript commit delay.
