# Current Product Scope (Canonical)

## What this repository is

`elevenlabs/packages` is a JavaScript/TypeScript SDK monorepo for the ElevenAgents platform and related realtime primitives.

## What is in scope

- Browser/client conversation sessions with agent IDs, signed URLs, or WebRTC conversation tokens
- React and React Native wrappers around conversation lifecycle
- Realtime speech-to-text client (`Scribe`) in `@elevenlabs/client`
- Generated protocol/message typings in `@elevenlabs/types`
- Widget packages for embedded experiences

## What is no longer in scope (at this commit)

- `agents-cli` package and associated test/workflow files were removed.
- Any internal docs/processes that depended on `packages/agents-cli` should be treated as historical and migrated.

## Recommended package selection

- Web app + voice agent: `@elevenlabs/react`
- Non-React browser app: `@elevenlabs/client`
- React Native app: `@elevenlabs/react-native`
- Typed event contracts and protocol-safe parsing: `@elevenlabs/types`
- Drop-in website agent widget: `@elevenlabs/convai-widget-embed`
