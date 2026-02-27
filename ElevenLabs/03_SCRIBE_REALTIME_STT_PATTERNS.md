# Scribe Realtime STT Patterns (Current)

`@elevenlabs/client` includes `Scribe` for websocket realtime transcription.

## Core connection

```ts
import { Scribe, AudioFormat, CommitStrategy } from "@elevenlabs/client";

const conn = Scribe.connect({
  token: process.env.ELEVENLABS_TEMP_TOKEN!,
  modelId: "scribe_v2_realtime",
  audioFormat: AudioFormat.PCM_16000,
  sampleRate: 16000,
  commitStrategy: CommitStrategy.VAD,
  languageCode: "en",
  includeTimestamps: true,
});

conn.on("partial_transcript", (e) => console.log("partial", e.text));
conn.on("committed_transcript", (e) => console.log("final", e.text));
conn.on("committed_transcript_with_timestamps", (e) => {
  console.log(e.text, e.words);
});
```

## Current operational guidance

- Prefer `PCM_16000` for low-latency subtitle pipelines.
- Set `languageCode` when known (conference/domain talk use cases).
- Use `previousText` only with first audio chunk after (re)connect.
- For live subtitles, treat partials as draft and committed transcripts as canonical.

## Commit strategy

- `manual`: your app controls segment finalization (`connection.commit()`).
- `vad`: server-side voice activity detection auto-commits segments.

For conference captions, start with `vad`, then tune thresholds if phrase clipping appears.
