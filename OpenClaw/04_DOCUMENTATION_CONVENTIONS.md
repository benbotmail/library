# Documentation Conventions

This file defines the conventions used across the OpenClaw LLM documentation pack. These conventions ensure consistency, clarity, and LLM retrievability.

## Structure and formatting

### Heading hierarchy
- `#` — Document title (only one per file)
- `##` — Major sections (concepts, workflows, reference)
- `###` — Subsections within major sections
- `####` — Specific details within subsections

**Avoid:**
- Skipping heading levels (e.g., `#` → `###` without `##`)
- Overly deep nesting (4+ levels) unless necessary

### Lists
Use **bullet lists** for unordered items and **numbered lists** for sequences.

**Bullet lists:**
- Item one
- Item two
- Item three

**Numbered lists:**
1. First step
2. Second step
3. Third step

**Nested lists:**
- Parent item
  - Child item
  - Another child
- Another parent

### Code blocks
Use fenced code blocks with language specification:

```bash
# Shell commands
openclaw gateway --port 18789
```

```json5
# Config snippets (JSON5)
{
  channels: {
    whatsapp: { allowFrom: ["+15555550123"] },
  },
}
```

```typescript
// TypeScript code examples
interface Config {
  channels: Record<string, ChannelConfig>;
}
```

### Inline code
Use backticks for:
- File paths: `~/.openclaw/openclaw.json`
- Config keys: `agents.defaults.model.primary`
- CLI commands: `openclaw doctor`
- Tool names: `exec`, `browser`, `web_search`
- Variables and placeholders: `<session-key>`, `<model-id>`

## Content guidelines

### Claim grounding
Every substantive claim should reference its source.

**Format:**
```markdown
## Section Title

[Content...]

**Source:** `path/to/source.md`, `src/file.ts`, or URL
**Last validated:** YYYY-MM-DD
```

**Examples:**
- **Source:** `docs/gateway/configuration.md`
- **Source:** `src/channels/whatsapp/channel.ts`
- **Source:** <https://docs.openclaw.ai/concepts/models>

### Provenance tracking
Track when documentation was last validated against upstream sources.

**Format:**
```markdown
## Provenance
- **Source:** [source file(s)]
- **Last validated:** YYYY-MM-DD (version/context)
```

**Example:**
```markdown
## Provenance
- **Source:** `docs/gateway/configuration.md`, `src/config/schema.ts`
- **Last validated:** 2026-03-18 (against openclaw@latest from GitHub)
```

### Procedural clarity
For workflows and procedures:
1. Start with prerequisites
2. Provide step-by-step instructions
3. Include expected outputs
4. Add troubleshooting notes

**Example:**
```markdown
## Setting up WhatsApp

**Prerequisites:**
- Node.js 22+ installed
- `openclaw` installed via npm
- WhatsApp account

**Steps:**
1. Login to WhatsApp:
   ```bash
   openclaw channels login whatsapp
   ```

2. Follow the QR code pairing flow in your terminal.

3. Verify connection:
   ```bash
   openclaw channels status
   ```

**Expected output:**
```
✓ WhatsApp: Connected (+15555550123)
```

**Troubleshooting:**
- If pairing fails, check that you have an active internet connection.
- For QR code issues, ensure your terminal supports UTF-8.
```

## LLM retrievability

### Title and filename conventions
Use descriptive titles that clearly indicate the document's purpose.

**Pattern:** `XX_DESCRIPTIVE_TITLE.md` where `XX` is a two-digit number for sorting.

**Examples:**
- `01_ARCHITECTURE_OVERVIEW.md` — System architecture
- `30_CHANNEL_OVERVIEW.md` — Channel reference
- `110_TROUBLESHOOTING_ENTRY_POINTS.md` — Troubleshooting guide

### Section headings
Use headings that contain keywords an LLM might query.

**Good:**
- `## Setting up WhatsApp`
- `## dmPolicy configuration`
- `## Troubleshooting connection failures`

**Avoid:**
- `## Setup` (too vague)
- `## Configuration` (not specific enough)
- `## Problems` (ambiguous)

### Inline keywords
Include relevant keywords in natural language prose.

**Example:**
> The `dmPolicy` field controls who can send DMs to the Gateway on a channel. When set to `"pairing"`, unknown senders receive a one-time pairing code that they must approve before messaging.

### Cross-references
Use explicit cross-references between related documents.

**Format:**
```markdown
See `30_CHANNEL_OVERVIEW.md` for channel-specific setup details.
Refer to `03_SOURCE_OF_TRUTH_MAP.md` for conflict resolution guidance.
```

### Tables and matrices
Use structured formats for comparisons and mappings.

**Example:**
```markdown
| dmPolicy | Behavior | When to use |
|----------|----------|-------------|
| `"pairing"` | One-time pairing code | Default for personal use |
| `"allowlist"` | Only allowlisted senders | Shared or public bots |
| `"open"` | Allow all senders | Rare; use with caution |
| `"disabled"` | Ignore DMs | Group-only bots |
```

## Code examples

### Command-line examples
Show the exact command with expected output.

**Format:**
```bash
openclaw gateway --port 18789
```

**Output:**
```
Gateway started on port 18789
Web UI: http://127.0.0.1:18789/
```

### Config examples
Use JSON5 (allows comments and trailing commas).

**Format:**
```json5
{
  channels: {
    whatsapp: {
      dmPolicy: "pairing",
      allowFrom: ["+15555550123"],
    },
  },
}
```

### Code snippets
Use appropriate language highlighting.

**Format:**
```typescript
interface ChannelConfig {
  dmPolicy: "pairing" | "allowlist" | "open" | "disabled";
  allowFrom: string[];
}
```

## Warnings and cautions

Use semantic callouts for important information.

### Warning
Use for critical issues that could cause problems:

```markdown
<Warning>
The Gateway will refuse to start if the config is invalid. Run `openclaw doctor` to diagnose issues.
</Warning>
```

### Tip
Use for helpful suggestions:

```markdown
<Tip>
Use `openclaw config set` for quick edits instead of manually editing `openclaw.json`.
</Tip>
```

### Note
Use for supplementary information:

```markdown
<Note>
The config file supports JSON5, so you can use comments and trailing commas.
</Note>
```

## Markdown compatibility

Avoid platform-specific features that don't render everywhere:

**Avoid:**
- HTML tables (use markdown tables)
- Raw HTML tags (unless necessary)
- Complex nested blockquotes
- Task lists that rely on checkboxes

**Use:**
- Standard markdown tables
- Fenced code blocks
- Standard heading hierarchy
- Lists and nested lists

## Version-specific notes

When behavior differs between versions:

**Format:**
```markdown
**Version note:** As of OpenClaw v2026.03.18, the `dmPolicy` default changed from `"open"` to `"pairing"`. Earlier versions may behave differently.
```

## Troubleshooting sections

For troubleshooting docs, follow this structure:

```markdown
## Symptom: [Description]

**Likely causes:**
1. Cause one
2. Cause two
3. Cause three

**Diagnostic steps:**
1. Run `command`
2. Check `file`
3. Verify `condition`

**Solutions:**
1. [Fix for cause 1]
2. [Fix for cause 2]
3. [Fix for cause 3]

**If unresolved:** Escalate to `113_ESCALATION_HANDOFF.md`
```

## Provenance
- **Source:** `01_DOCUMENTATION_CONVENTIONS.md` (this file)
- **Last validated:** 2026-03-18
