# Architecture

FlowLink is a distributed system built with Rust for performance and safety.

## System Overview

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

## Components

### Relay

Central WebSocket server that connects all AI agents. Handles:
- Agent registration and authentication
- Message routing between agents
- Config hot-reload via file watcher
- Rate limiting (100 req/10s per IP)

### Agent

Runs on the protected server. Responsible for:
- Command interception before execution
- Local threat analysis
- Policy evaluation
- Audit logging (triple-write: memory + file + database)

### Dashboard

Web interface for monitoring and management:
- Real-time agent status
- Audit trail viewer
- Policy editor
- Approval queue (human-in-the-loop)

## 7 Levels of Protection

| Level | Name | What It Does | Example |
|-------|------|-------------|---------|
| 1 | Pattern Matching | Regex + custom rules on command text | Detects `rm -rf`, `curl \| bash` |
| 2 | Risk Scoring | 0-100 score per command | `ssh-keygen` = 15, `cat /etc/shadow` = 95 |
| 3 | Policy Engine | Declarative rules (per-agent, per-org) | "Block all network commands after 18:00" |
| 4 | Approval Workflow | Human-in-the-loop for dangerous commands | Slack/Telegram notification |
| 5 | Pattern Learning | Auto-detects new threat patterns | Learns from blocked commands across users |
| 6 | Shield | Kernel-level interception (eBPF) | Intercepts before `execve()`, cannot be bypassed |
| 7 | MCP Gateway | Tool-level access control | Restrict which MCP tools each agent can use |

## Message Flow

```
AI Agent sends command
        │
        ▼
┌─ Level 1: Pattern Match ──── match? ──▶ Block
│
├─ Level 2: Risk Score ─────── score > threshold? ──▶ Block/Warn
│
├─ Level 3: Policy Check ───── policy deny? ──▶ Block
│
├─ Level 4: Approval Queue ─── needs human? ──▶ Hold for review
│
├─ Level 5: Pattern Learning ── new pattern? ──▶ Learn + allow
│
├─ Level 6: Shield (eBPF) ──── kernel-level check
│
├─ Level 7: MCP Gateway ────── tool access control
│
└─ Allow → Execute → Log to audit trail
```

## Technology Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Relay | Rust + axum | WebSocket server, REST API |
| Agent | Rust | Command interception, local analysis |
| Dashboard | Next.js | Web interface |
| Database | PostgreSQL | Policies, audit trail, billing |
| Kernel | eBPF (Linux) | Shield: kernel-level interception |
| Encryption | X25519 + AES-256-GCM | E2EE between agent and relay |
| Transport | WebSocket + HTTPS | Real-time communication |
