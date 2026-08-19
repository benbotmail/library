# Cron and Scheduling

> Current as of 2026-08-19 (upstream `7a82d8b0f25`).

## Overview

OpenClaw supports scheduled automation via cron jobs. Cron jobs are persisted automation entries that execute configured payloads on their own schedule. The Automations scheduler also owns heartbeat cadence.

## Configuration

### Cron Jobs

```json5
{
  cron: {
    enabled: true,             // master toggle; false disables all scheduled work
    jobs: [
      {
        id: "daily-report",
        name: "Daily Report",
        schedule: { kind: "cron", expr: "0 9 * * *" },
        payload: {
          kind: "agentTurn",
          message: "Generate daily report",
        },
        sessionTarget: "isolated",  // isolated | main
        enabled: true,
      },
      {
        id: "quick-reminder",
        name: "Quick Reminder",
        schedule: { kind: "once", in: "20m" },   // one-shot: in 20 minutes
        payload: { kind: "agentTurn", message: "Meeting starts soon" },
        sessionTarget: "isolated",
      },
    ],
  },
}
```

### Schedule Kinds

| Kind | Format | Description |
|------|--------|-------------|
| `cron` | `{ kind: "cron", expr: "0 9 * * *" }` | Standard cron expression |
| `once` | `{ kind: "once", in: "20m" }` or `{ kind: "once", at: "2026-08-13T15:00:00Z" }` | One-shot reminder |

### Payload Kinds

| Kind | Description |
|------|-------------|
| `agentTurn` | Inject a message as a user turn into a session |
| (extensible via plugins) | Custom payload kinds from automation plugins |

### Session Targets

| Target | Behavior |
|--------|----------|
| `isolated` | Fresh ephemeral session (no conversation history); pruned after `cron.sessionRetention` (default `24h`) |
| `main` | Enqueue a system event into the owning agent's **main session** — processed with that session's existing context and last delivery context |
| `current` | Bound to the creating session at job-creation time |
| `session:<id>` | Persistent named session; context accumulates across runs (e.g. daily standups) |

**Freshness rule:** internal automation turns (main-session events) do **not** extend daily or idle reset freshness; only visible user activity updates session freshness.

### Run Completion Semantics

- Run history records payload execution in `status` (`ok` \| `error` \| `skipped`) and whole-run completion in `completionStatus` (`succeeded` \| `failed` \| `unknown`).
- Delivery is "required" only when the job explicitly sets `delivery.bestEffort: false`. A delivery-only failure leaves execution `status: "ok"`, does not increment error counters or retry backoff, and records `completionStatus: "failed"`.
- One-shot jobs (`--at`) auto-delete **only** when `completionStatus` is `succeeded`; pass `--keep-after-run` to keep successful runs. A required-delivery failure or unknown completion keeps the job disabled for inspection and restart recovery without replaying the payload.
- `openclaw automations run <jobId> --wait` exits `0` only for `completionStatus: "succeeded"`; errors, skipped runs, and wait timeouts exit non-zero (default timeout `10m`, poll `2s`).
- Direct Gateway event sources can use the `cron.run` API with `mode: "if-enabled"` to run immediately without overriding an operator-disabled or auto-disabled job; explicit operator run-now commands use `force`.

### Triggers, Script Payloads, and Stream Schedules

Event-driven automation runs **by default** (`cron.triggers.enabled: true`):

- **Condition triggers** — scripts evaluated against gateway state
- **`script` payloads** — headless code-mode execution (no conversational agent turn); only `main` and `isolated` session targets
- **Stream schedules** — a long-lived operator-authored argv command whose stdout/stderr lines fire the job; disabling the job stops the process; 5 consecutive runs < 60s error-caps the job (manual re-enable clears it)

**Security:** these surfaces run unattended with the owning agent's **full tool policy, including `exec`**. Set `cron.triggers.enabled: false` for a hard stop (disables creation and execution of all three). The `cron` block is strict: only `enabled`, `triggers`, `webhookToken`, `webhookSsrfPolicy`, `sessionRetention`, and `failureAlert` keys are accepted.

### Choosing a Model for Jobs

Pick the model for the job's difficulty, not the agent's default. Routine automation (summaries, triage, classification, status checks) runs well on a **lighter model** — cheaper and faster per run, and the savings compound across a schedule. Keep the default model for deep-reasoning jobs; use `--fallbacks` when a light primary should escalate on failure.

### Cron Expression Format

```
┌──────── minute (0-59)
│ ┌────── hour (0-23)
│ │ ┌──── day of month (1-31)
│ │ │ ┌── month (1-12)
│ │ │ │ ┌ day of week (0-7, 0 and 7 = Sunday)
│ │ │ │ │
* * * * *
```

### Common Patterns

| Schedule | Expression |
|----------|------------|
| Every 30 min | `*/30 * * * *` |
| Hourly | `0 * * * *` |
| Daily 9 AM | `0 9 * * *` |
| Weekdays 9 AM | `0 9 * * 1-5` |
| Weekly Monday | `0 9 * * 1` |
| Monthly 1st | `0 9 1 * *` |

## Heartbeat vs Cron

| Use Cron When | Use Heartbeat When |
|---------------|-------------------|
| Exact timing matters ("9:00 AM every Monday") | Multiple checks batch together |
| Task needs isolation from main session | Conversational context needed |
| Different model or thinking level | Timing can drift slightly |
| One-shot reminders ("remind me in 20 minutes") | Reducing API call count by combining checks |
| Output should deliver directly to a channel | Background periodic checks |

**Key detail:** Scheduled heartbeats require `cron.enabled: true`. When cron is disabled, the gateway logs a startup warning and does not run scheduled heartbeats. Manual and event-driven heartbeat wakes remain available.

## Heartbeat as Automation

Heartbeat cadence is owned by the Automations scheduler:
- The gateway maintains one system-owned automation job per heartbeat-enabled agent
- Visible in `openclaw cron list --all` as `Heartbeat (agent-id)`
- Heartbeat config (`agents.*.heartbeat`) is the desired-state input
- The persisted monitor schedule owns the actual tick
- `openclaw doctor --fix` can materialize missing or stale monitor rows

### Heartbeat config keys (`agents.defaults.heartbeat` / `agents.entries.*.heartbeat`)

Strict schema — only these fields are accepted:

| Key | Type / Values | Default | Notes |
|-----|---------------|---------|-------|
| `agentId` | agent id | — | `agents.defaults.heartbeat` only: explicit owner for ambient heartbeat runs when no per-agent `heartbeat` block exists; without it, a shared block enrolls all agents |
| `every` | duration (`0m` disables) | `30m` (Anthropic OAuth/token auth bumps to `1h` while unset) | Cadence |
| `target` | `owner` \| `last` \| channel id \| `none` | `owner` | `owner` = first `commands.ownerAllowFrom` entry, then channel `allowFrom`; never a group. `last` follows most recent conversation incl. groups. `none` = internal only |
| `directPolicy` | `allow` \| `block` | `allow` | `block` suppresses direct/DM delivery (`reason=dm-blocked`) while still running the turn |
| `to` | string | — | Recipient for explicit channel target (E.164, Telegram chat id; topics via `<chatId>:topic:<threadId>`) |
| `accountId` | string | — | Multi-account channel account id (validated when `target: "last"`) |
| `prompt` | string | built-in monitor prompt | Overrides default prompt body (not merged) |
| `timeoutSeconds` | number | `agents.defaults.timeoutSeconds` else `min(every, 600)` | Max turn duration |
| `activeHours` | `{ start, end, timezone? }` | — | HH:MM window; `end` exclusive (`24:00` allowed); timezone `user`/`local`/IANA |
| `session` | session key | main session | Run context only; delivery is `target`/`to` |
| `lightContext` | boolean | — | Skip workspace bootstrap files for heartbeat runs |
| `isolatedSession` | boolean | — | Fresh session each run (no conversation history) |

Alert/visibility flags (`showOk`, `showAlerts`, `useIndicator`) control skip behavior; if all three are disabled the run is skipped up front (`reason=alerts-disabled`).

## Wake Events

- Cron jobs can trigger Gateway wake events when daemon would otherwise be idle
- Agent executes turn with payload as context
- Session history updated normally
- Gateway returns to idle state

## CLI

```bash
openclaw cron list                  # List active cron jobs
openclaw cron list --all            # Include system-owned (heartbeat) jobs
openclaw cron scratch <jobId> --set "..."  # Set monitor scratch
openclaw cron run <jobId>           # Manually trigger a job
```

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Cron not firing | `cron.enabled: false` or schedule expression wrong | Check config; run `openclaw cron list` |
| Heartbeat not running | `cron.enabled: false` or heartbeat disabled | Enable cron; check `agents.*.heartbeat.every` |
| Wrong timezone | Cron uses configured timezone | Set `agents.defaults.userTimezone` |
| Payload not delivered | Channel or agent misconfigured | Verify agent ID and channel routing |
| Stale monitor jobs | Schema changes after upgrade | `openclaw doctor --fix` |

## Related

- [Automation docs](https://docs.openclaw.ai/automation)
- [Heartbeat docs](https://docs.openclaw.ai/gateway/heartbeat)
- [Configuration](https://docs.openclaw.ai/gateway/configuration)
