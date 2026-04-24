# Getting Started

Install FlowLink and protect your first AI agent in under 2 minutes.

## System Requirements

- Linux (amd64 or arm64)
- Network access to `flowlink.flow-masters.ru`
- An AI coding agent (Claude Code, Cursor, Copilot, Windsurf)

## Step 1 — Download

```bash
curl -fsSL https://flowlink.flow-masters.ru/downloads/flowlink-linux-amd64 \
  -o /usr/local/bin/flowlink && chmod +x /usr/local/bin/flowlink
```

Verify:

```bash
flowlink --version
```

## Step 2 — Register

1. Go to [flowlink.flow-masters.ru/login](https://flowlink.flow-masters.ru/login)
2. Create a free account (no credit card)
3. Copy your API key from the dashboard

## Step 3 — Run Agent

```bash
flowlink agent --api-key YOUR_KEY --relay wss://flowlink.flow-masters.ru/ws
```

Your agent is now connected. Every command passes through FlowLink's analysis pipeline.

## Step 4 — Test Protection

Try running a dangerous command through your AI agent:

```
# Ask your agent to execute this:
rm -rf /
```

FlowLink will block it. Check the dashboard to see:
- Risk score: 98/100
- Triggered rule: destructive_command
- Decision: blocked
- Full audit trail entry

## Optional — Systemd Service

For persistent protection, install as a systemd service:

```bash
sudo tee /etc/systemd/system/flowlink.service > /dev/null <<EOF
[Unit]
Description=FlowLink Agent
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/flowlink agent \
  --api-key YOUR_KEY \
  --relay wss://flowlink.flow-masters.ru/ws
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable flowlink
sudo systemctl start flowlink
```

## MCP Setup (Cursor / Windsurf)

Add to your MCP configuration:

```json
{
  "mcpServers": {
    "flowlink": {
      "url": "https://flowlink.flow-masters.ru/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_KEY"
      }
    }
  }
}
```

## Configuration

Create a policy file for custom rules:

```yaml
policies:
  - name: no-network-after-hours
    agents: ["claude-code-*"]
    actions: ["network"]
    schedule: "18:00-09:00"
    decision: block
    message: "Network access blocked outside business hours"

  - name: allow-read-only
    agents: ["*"]
    actions: ["read"]
    decision: allow
```

See [Deployment](deployment.md) for full configuration reference.

## Next Steps

- [Architecture Overview](architecture.md) — understand how FlowLink works
- [API Reference](api.md) — REST and WebSocket API
- [Comparison](comparison.md) — FlowLink vs alternatives
- [Security](security.md) — security practices and reporting
