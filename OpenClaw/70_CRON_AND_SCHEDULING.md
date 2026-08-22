# Cron and Scheduling

## Scope
Cron jobs: schedules, payloads, delivery modes, wake events, and reminders. How to configure time-based automation in OpenClaw.

## Audience
- Operators setting up scheduled tasks and reminders
- LLM systems retrieving OpenClaw scheduling patterns

## Cron Overview

OpenClaw supports scheduled message delivery via cron expressions. Cron payloads are injected into sessions as user messages at configured times.

## Configuration

### Basic Cron Job
```yaml
cron:
  entries:
    - schedule: "0 9 * * 1"        # Every Monday 9:00 AM
      message: "Weekly status check"
      agent: "main"
    - schedule: "*/30 * * * *"     # Every 30 minutes
      message: "Heartbeat poll"
      agent: "main"
```

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

| Schedule | Expression | Description |
|----------|------------|-------------|
| Every 30 min | `*/30 * * * *` | Heartbeat-style polling |
| Hourly | `0 * * * *` | Top of every hour |
| Daily 9 AM | `0 9 * * *` | Morning briefing |
| Weekdays 9 AM | `0 9 * * 1-5` | Workday morning check |
| Weekly Monday | `0 9 * * 1` | Monday morning summary |
| Monthly 1st | `0 9 1 * *` | Monthly review |

## Delivery Modes

| Mode | Behavior | Use Case |
|------|----------|----------|
| **Session message** | Delivered to existing main session | Periodic checks, heartbeat polls |
| **New session** | Creates ephemeral session for task | Isolated tasks, one-shot reminders |
| **Channel delivery** | Sent directly to configured channel | Notifications, summaries to Discord/Telegram |

## Reminders

One-shot reminders can be set without full cron syntax:

```yaml
cron:
  entries:
    - in: "20m"                    # 20 minutes from now
      message: "Meeting starts in 10 minutes"
      channel: "telegram"
    - at: "2026-04-11T09:00:00Z"  # Specific time
      message: "Deploy release v2.0"
```

## Wake Events

Cron jobs can trigger Gateway wake events when the daemon would otherwise be idle:

- Gateway wakes, processes scheduled payload
- Agent executes turn with payload as context
- Session history updated normally
- Gateway returns to idle state

## When to Use Cron vs Heartbeat

| Use Cron When | Use Heartbeat When |
|---------------|-------------------|
| Exact timing matters | Multiple checks batch together |
| Task needs isolation | Conversational context needed |
| Different model/thinking level | Timing can drift slightly |
| One-shot reminders | Reducing API call count |
| Standalone channel delivery | Background periodic checks |

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Cron not firing | Schedule expression wrong | Validate with `openclaw cron list` |
| Wrong timezone | Cron uses UTC by default | Adjust schedule expression for timezone offset |
| Duplicate firings | Overlapping cron entries | Check for duplicate schedules in config |
| Payload not delivered | Channel or agent misconfigured | Verify agent ID and channel routing |

## Commands

```bash
# List active cron jobs
openclaw cron list

# Validate cron config
openclaw config validate

# Test cron expression
openclaw cron test "*/30 * * * *"
```

## Related Documentation

- `70_CRON_AND_SCHEDULING.md` — This document
- `71_HOOKS_AND_AUTOMATION.md` — Event-driven automation
- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Full config schema
- `44_MEMORY_AND_CONTEXT.md` — Memory maintenance via cron

## Provenance

- Cron scheduler from `packages/gateway/src/cron.ts`
- Schedule parsing from `node-cron` or equivalent
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
