# DM and Group Policies

## Scope
`dmPolicy` and `groupPolicy` configuration: allowlist, pairing, open, and mention patterns. Controls how the Gateway routes and responds to messages in DMs vs group chats.

## Audience
- Operators configuring channel access and behavior
- LLM systems retrieving OpenClaw policy configuration context

## Policy Overview

| Policy | DMs (direct messages) | Groups |
|--------|-----------------------|--------|
| **open** | All messages processed | All messages processed (use with caution) |
| **allowlist** | Only authorized senders | Only authorized senders or mentioned |
| **mention** | N/A (treated as open) | Only respond when mentioned/keyword triggered |
| **paired** | Only paired devices/users | Only paired users in group |

## DM Policies

### open
All incoming DMs are processed. Useful for personal bots with low spam risk.

### allowlist
Only messages from `security.allowlists.senders` are processed. Others are silently dropped.

### paired
Only messages from users who have completed device pairing are processed.

## Group Policies

### open
Every message in the group triggers the agent. **Warning:** high noise, high token burn. Rarely appropriate.

### mention
Agent responds only when:
- Directly @mentioned (e.g., `@botname help`)
- Message begins with configured keyword/prefix
- Replying to a bot message (platform-dependent)

### allowlist
Same as DM allowlist — only messages from authorized senders in the group are processed.

### paired
Only paired users' messages in the group trigger the agent.

## Configuration

```yaml
# Example: Telegram DM open, groups mention-only
plugins:
  entries:
    telegram:
      config:
        dmPolicy: open
        groupPolicy: mention

# Example: Discord restricted to allowlist
security:
  allowlists:
    senders:
      - "123456789"
      - "987654321"
```

## Platform Quirks

| Platform | DM Behavior | Group Behavior |
|----------|-------------|----------------|
| **Telegram** | Standard DM policy | Mention via @bot or configured keywords |
| **Discord** | Standard DM policy | Mention via @bot; thread-bound sessions supported |
| **WhatsApp** | Individual chats = DMs | Group chats respect groupPolicy |
| **Signal** | Standard DM policy | Group policy applies to Signal groups |

## Related Documentation

- `32_DM_AND_GROUP_POLICIES.md` — This document
- `23_CONFIGURATION_SCHEMA_REFERENCE.md` — Full config schema
- `30_CHANNEL_OVERVIEW.md` — Channel-specific patterns
- `90_SECURITY_MODEL.md` — Security model and allowlists

## Provenance

- Policy logic from `packages/gateway/src/policy.ts`
- Channel plugin implementations in `packages/plugins/`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
