# Typed Events and Protocol Contracts

Use `@elevenlabs/types` to keep event handling aligned with upstream AsyncAPI schema updates.

## Import model

```ts
import { Incoming, Outgoing } from "@elevenlabs/types";

function handleIncoming(msg: Incoming.AudioClientEvent | Incoming.UserTranscriptionClientEvent) {
  // exhaustive handling in your app
}

const out: Outgoing.UserMessage = {
  reservedType: "user_message",
  reservedText: "Hello",
};
```

## Why this matters

- Prevents drift between internal event assumptions and platform contracts.
- Makes refactors safer when message fields evolve.
- Improves IDE/documentation quality for handlers.
