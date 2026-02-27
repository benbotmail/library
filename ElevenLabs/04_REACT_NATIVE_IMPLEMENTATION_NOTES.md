# React Native Implementation Notes

## Required dependencies

`@elevenlabs/react-native` requires:

- `@livekit/react-native`
- `@livekit/react-native-webrtc`
- `livekit-client`

Expo Go is not supported; use development builds.

## Provider + hook skeleton

```tsx
import { ElevenLabsProvider, useConversation } from "@elevenlabs/react-native";

function App() {
  return (
    <ElevenLabsProvider audioSessionConfig={{ allowMixingWithOthers: true }}>
      <Screen />
    </ElevenLabsProvider>
  );
}

function Screen() {
  const c = useConversation();
  // c.startSession(), c.endSession(), c.sendUserMessage(), c.sendFeedback()
  return null;
}
```

## Mobile UX defaults

- Ask for microphone permission with clear pre-prompt context.
- Add explicit reconnect UI for weak-network conditions.
- Reflect conversation status (`connected/connecting/disconnected`) in persistent UI.
