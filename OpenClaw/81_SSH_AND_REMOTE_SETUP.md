# SSH and Remote Setup

## Scope
Remote Gateway access via SSH tunneling, systemd, and systemd user services. How to run OpenClaw remotely and connect to it.

## Audience
- Operators deploying Gateways on remote servers
- LLM systems retrieving remote access patterns

## Access Patterns

| Pattern | Use Case | Complexity |
|---------|----------|------------|
| **SSH tunnel** | Occasional remote access from local machine | Low |
| **systemd service** | Persistent Gateway on remote server | Medium |
| **systemd user service** | Gateway runs under user account (no root) | Medium |
| **Tailscale VPN** | Always-on access across networks | Medium |
| **Reverse tunnel** | Server behind NAT/firewall | High |

## SSH Tunneling

### Quick One-Off Tunnel
```bash
# Forward remote Gateway port (8080) to local (8080)
ssh -L 8080:localhost:8080 user@remote-server

# Or for CLI access only
ssh user@remote-server
```

Now use `openclaw` CLI locally with `--gateway-url=http://localhost:8080` or set env:
```bash
export OPENCLAW_GATEWAY_URL="http://localhost:8080"
```

### Background Tunnel (Persistent)
```bash
# Start tunnel in background
ssh -N -L 8080:localhost:8080 user@remote-server &

# Or use autossh for auto-reconnect
autossh -M 0 -N -L 8080:localhost:8080 user@remote-server &
```

## Systemd Service Setup

### System-wide Service (Root)

Create `/etc/systemd/system/openclaw-gateway.service`:
```ini
[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
User=openclaw
WorkingDirectory=/opt/openclaw
ExecStart=/usr/local/bin/openclaw gateway start
Restart=on-failure
RestartSec=10
Environment="NODE_ENV=production"

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl daemon-reload
sudo systemctl enable openclaw-gateway
sudo systemctl start openclaw-gateway
sudo systemctl status openclaw-gateway
```

### User Service (No Root)

Create `~/.config/systemd/user/openclaw-gateway.service`:
```ini
[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
WorkingDirectory=/home/user/.npm-global/lib/node_modules/openclaw
ExecStart=/home/user/.npm-global/bin/openclaw gateway start
Restart=on-failure
RestartSec=10

[Install]
WantedBy=default.target
```

Enable and start:
```bash
systemctl --user daemon-reload
systemctl --user enable openclaw-gateway
systemctl --user start openclaw-gateway
systemctl --user status openclaw-gateway
```

Ensure lingering enabled (auto-start on boot):
```bash
loginctl enable-linger user
```

## Logs

### System-wide Service
```bash
# View logs
sudo journalctl -u openclaw-gateway -f

# Last 100 lines
sudo journalctl -u openclaw-gateway -n 100
```

### User Service
```bash
# View logs
journalctl --user -u openclaw-gateway -f

# Last 100 lines
journalctl --user -u openclaw-gateway -n 100
```

## Reverse Tunnels

For servers behind NAT/firewall, create reverse tunnel from server to accessible host:

On server (behind NAT):
```bash
ssh -N -R 8080:localhost:8080 user@public-host
```

On public host, connect to Gateway via `http://localhost:8080`.

Use autossh for persistence:
```bash
autossh -M 0 -N -R 8080:localhost:8080 user@public-host &
```

## Gateway URL Configuration

When accessing via SSH tunnel or reverse tunnel, configure OpenClaw to use local URL:

```bash
# Environment variable
export OPENCLAW_GATEWAY_URL="http://localhost:8080"

# CLI flag
openclaw gateway status --gateway-url=http://localhost:8080

# Config file
gateway:
  bind:
    host: "0.0.0.0"
    port: 8080
```

## Firewall Considerations

- If accessing remotely without SSH tunnel, open Gateway port in firewall
- Prefer SSH tunnel for security (no open ports)
- Use SSH keys instead of passwords
- Consider `ufw` or `firewalld` for firewall management

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| SSH tunnel closes immediately | Gateway not running or wrong port | Verify `openclaw gateway status` on remote |
| Service fails to start | Permission or path issues | Check journalctl logs, verify paths |
| Tunnel won't connect | Firewall or SSH config issue | Check `sshd` config, firewall rules |
| Can't access Gateway after reboot | Service not enabled | `systemctl enable openclaw-gateway` |

## Related Documentation

- `81_SSH_AND_REMOTE_SETUP.md` — This document
- `80_LOCALHOST_AND_PORTS.md` — Port configuration and binding
- `82_TAILSCALE_AND_VPN_ACCESS.md` — VPN-based access
- `103_DIAGNOSTICS_AND_HEALTH.md` — Diagnostic data collection

## Provenance

- systemd service patterns from standard Linux administration
- SSH tunneling from OpenSSH documentation
- OpenClaw CLI from `packages/cli/`
- Official docs: <https://docs.openclaw.ai>
- Repository: <https://github.com/openclaw/openclaw>
