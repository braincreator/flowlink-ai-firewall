# Deployment

## Binary Installation

### Download

```bash
curl -fsSL https://flowlink.flow-masters.ru/downloads/flowlink-linux-amd64 \
  -o /usr/local/bin/flowlink && chmod +x /usr/local/bin/flowlink
```

### Verify

```bash
flowlink --version
```

### Systemd Service

```bash
sudo tee /etc/systemd/system/flowlink.service > /dev/null <<'EOF'
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
Environment=RUST_LOG=info

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable flowlink
sudo systemctl start flowlink
sudo systemctl status flowlink
```

## Docker

```bash
docker run -d \
  --name flowlink \
  --restart always \
  -e FLOWLINK_API_KEY=YOUR_KEY \
  -e FLOWLINK_RELAY=wss://flowlink.flow-masters.ru/ws \
  braincreator/flowlink:latest
```

### Docker Compose

```yaml
services:
  flowlink:
    image: braincreator/flowlink:latest
    container_name: flowlink
    restart: always
    environment:
      - FLOWLINK_API_KEY=${FLOWLINK_API_KEY}
      - FLOWLINK_RELAY=wss://flowlink.flow-masters.ru/ws
      - RUST_LOG=info
```

## Kubernetes

### Install CRD

```bash
kubectl apply -f https://flowlink.flow-masters.ru/config/crd.yaml
```

### Deploy Agent

```bash
kubectl apply -f - <<EOF
apiVersion: flowlink.io/v1
kind: FlowLinkShieldPolicy
metadata:
  name: prod-policy
spec:
  agentConfig:
    relay: wss://flowlink.flow-masters.ru/ws
    apiKeySecret:
      name: flowlink-credentials
      key: api-key
  policyRules:
    - name: block-destructive
      pattern: "rm -rf|mkfs|dd if="
      decision: block
    - name: allow-read-only
      actions: ["read"]
      decision: allow
EOF
```

## Configuration Reference

### CLI Flags

| Flag | Description | Default |
|------|-------------|---------|
| `--api-key` | API key for authentication | — |
| `--relay` | WebSocket relay URL | `wss://flowlink.flow-masters.ru/ws` |
| `--config` | Path to config file | `flowlink.json` |
| `--log-level` | Log level (trace, debug, info, warn, error) | `info` |

### Config File (flowlink.json)

```json
{
  "relay": "wss://flowlink.flow-masters.ru/ws",
  "api_key": "YOUR_KEY",
  "agent_id": "my-agent",
  "hostname": "prod-server-1",
  "policies": {
    "file": "/etc/flowlink/policies.yaml"
  },
  "audit": {
    "file": "/var/log/flowlink/audit.jsonl",
    "rotate_size_mb": 100,
    "keep_files": 30
  }
}
```

### Environment Variables

| Variable | Description |
|----------|-------------|
| `FLOWLINK_API_KEY` | API key (overrides config) |
| `FLOWLINK_RELAY` | Relay URL (overrides config) |
| `RUST_LOG` | Rust log level |
| `FLOWLINK_CONFIG` | Path to config file |
