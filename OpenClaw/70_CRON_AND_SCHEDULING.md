# Cron and Scheduling

> Current as of 2026-08-13 (upstream `0926d56cbf9`).

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
| `isolated` | Fresh ephemeral session (no conversation history) |
| `main` | Deliver to agent's main session |

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
