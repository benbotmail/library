# 10 — SDK Migration Guide (v1.0 Preview)

This document summarizes the upcoming breaking changes for the next major version of ElevenLabs SDK packages. The migration skill is maintained upstream at `.agents/skills/elevenlabs:sdk-migration/SKILL.md`.

> **Status**: Preview — install RC versions with `npm install @elevenlabs/client@next @elevenlabs/react@next`

---

## 1) High-level breaking changes

| Package | Change | Impact |
|---------|--------|--------|
| `@elevenlabs/client` | `Conversation` no longer a class | `instanceof` checks break; subclassing removed |
| `@elevenlabs/client` | `Input`/`Output` classes removed | Use methods on conversation object instead |
| `@elevenlabs/react` | `useConversation` requires `ConversationProvider` | Must wrap components in provider |
| `@elevenlabs/react` | Granular hooks introduced | Better render performance via state slicing |
| `@elevenlabs/react-native` | Complete API rewrite | Re-exports from `@elevenlabs/react` |

---

## 2) `@elevenlabs/client` changes

### `Conversation` is now a namespace + type alias

```ts
// BEFORE (v0.x)
if (session instanceof Conversation) { /* ... */ }

// AFTER (v1.0)
if ("changeInputDevice" in session) {
  // session is VoiceConversation
}
```

### `Input` and `Output` classes removed

```ts
// BEFORE (v0.x)
const input: Input = conversation.input;
input.analyser.getByteFrequencyData(data);
input.setMuted(true);

// AFTER (v1.0)
conversation.getInputByteFrequencyData();
conversation.setMicMuted(true);
```

### Wake lock is now private

Opt out with `useWakeLock: false` in session options.

---

## 3) `@elevenlabs/react` changes

### Provider required

```tsx
// BEFORE (v0.x)
function App() {
  const { status, startSession } = useConversation({ agentId: "..." });
  return <div>...</div>;
}

// AFTER (v1.0)
function App() {
  return (
    <ConversationProvider>
      <Conversation />
    </ConversationProvider>
  );
}
```

### Granular hooks for better performance

| Hook | Returns |
|------|---------|
| `useConversationControls()` | Stable action methods (never re-render) |
| `useConversationStatus()` | `status`, `message` |
| `useConversationInput()` | `isMuted`, `setMuted` |
| `useConversationMode()` | `mode`, `isSpeaking`, `isListening` |
| `useConversationFeedback()` | `canSendFeedback`, `sendFeedback` |
| `useRawConversation()` | Raw `Conversation` instance (escape hatch) |

### Dynamic client tool registration

```tsx
import { useConversationClientTool } from "@elevenlabs/react";

// Untyped
useConversationClientTool("get_weather", params => {
  return `Weather in ${params.city} is sunny.`;
});

// Type-safe
type Tools = {
  get_weather: (params: { city: string }) => string;
};

useConversationClientTool<Tools>("get_weather", params => {
  return `Weather in ${params.city} is sunny.`;
});
```

---

## 4) `@elevenlabs/react-native` changes

The custom LiveKit-based implementation is removed. The package now re-exports from `@elevenlabs/react` with platform-specific side-effects (WebRTC polyfills, AudioSession config).

```tsx
// BEFORE (v0.x)
import { ElevenLabsProvider, useConversation } from "@elevenlabs/react-native";

// AFTER (v1.0)
import {
  ConversationProvider,
  useConversationControls,
  useConversationStatus,
} from "@elevenlabs/react-native";
```

---

## 5) Migration checklist

- [ ] Wrap all `useConversation` usages in `ConversationProvider`
- [ ] Replace `instanceof Conversation` with duck-typing
- [ ] Replace `conversation.input/output` access with method calls
- [ ] Update React Native imports from `ElevenLabsProvider` to `ConversationProvider`
- [ ] Consider granular hooks for render-critical components
- [ ] Test wake lock behavior if previously managing manually

---

## 6) Canonical source

The authoritative migration guide is maintained upstream:
- `open-source/elevenlabs/.agents/skills/elevenlabs:sdk-migration/SKILL.md`

This document is a snapshot for local reference.
