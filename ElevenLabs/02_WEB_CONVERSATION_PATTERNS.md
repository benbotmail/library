# Web Conversation Patterns (Current)

## Session start choices

Use exactly one auth pattern:

1. **Public agent**: `agentId`
2. **Private + WebSocket**: backend mints `signed_url`
3. **Private + WebRTC**: backend mints `conversation token`

Never expose `xi-api-key` to browser clients.

## Minimal React pattern

```tsx
import { useConversation } from "@elevenlabs/react";

export function AgentPanel() {
  const convo = useConversation({
    onStatusChange: (s) => console.log("status", s),
    onMessage: (m) => console.log("msg", m),
    onError: (e) => console.error(e),
  });

  async function start() {
    await navigator.mediaDevices.getUserMedia({ audio: true });
    await convo.startSession({
      agentId: process.env.NEXT_PUBLIC_AGENT_ID!,
      connectionType: "webrtc",
      userId: "user-123",
    });
  }

  return <button onClick={start}>Start</button>;
}
```

## High-value callbacks to wire first

- `onStatusChange` for UI state machine
- `onModeChange` for speaking/listening indicators
- `onInterruption` to handle barge-in UX
- `onCanSendFeedbackChange` for feedback controls
- `onUnhandledClientToolCall` for observability on tool mismatches

## Text-only mode

When building chat-first interfaces, enable `textOnly` to avoid microphone prompts and audio graph setup.
