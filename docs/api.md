# API Reference

## Authentication

All API requests require an API key in the header:

```
Authorization: Bearer YOUR_API_KEY
```

## Rate Limiting

100 requests per 10 seconds per IP. Health and WebSocket endpoints are whitelisted.

---

## REST API

### Health Check

| Method | Path | Description |
|--------|------|-------------|
| GET | `/healthz` | Service health check |

Response:

```json
{
  "status": "ok",
  "version": "1.0.0"
}
```

### Agents

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/agents` | List connected agents |
| GET | `/api/agents/:id` | Get agent details |
| POST | `/api/agents/:id/commands` | Send command to agent |
| DELETE | `/api/agents/:id` | Disconnect agent |

### Commands

| Method | Path | Description |
|--------|------|-------------|
| POST | `/api/agents/:id/commands` | Send command to agent |
| GET | `/api/agents/:id/commands` | Command history |

### Billing

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/billing/usage` | Usage statistics |
| GET | `/api/billing/plan` | Current plan details |
| GET | `/api/billing/invoices` | Invoice history |

### Devices

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/devices` | List registered devices |
| GET | `/api/devices/:id/trust` | Device trust score (0-100) |
| POST | `/api/devices/:id/trust` | Update trust score |

### Audit

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/audit` | Audit events (paginated) |
| GET | `/api/audit/export` | Export audit (CEF, LEEF, JSON) |
| GET | `/api/audit/stats` | Audit statistics |

### Policies

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/policies` | List policies |
| POST | `/api/policies` | Create policy |
| PUT | `/api/policies/:id` | Update policy |
| DELETE | `/api/policies/:id` | Delete policy |

---

## WebSocket API

Connect to: `wss://flowlink.flow-masters.ru/ws`

### Message Types

**Agent Registration**

```json
{
  "type": "register",
  "api_key": "YOUR_KEY",
  "agent_id": "claude-code-1",
  "hostname": "prod-server-1"
}
```

**Command Submission**

```json
{
  "type": "command",
  "command": "ls -la /tmp",
  "agent_id": "claude-code-1"
}
```

**Analysis Result**

```json
{
  "type": "result",
  "decision": "allow",
  "risk_score": 5,
  "rules_triggered": [],
  "command_id": "cmd_abc123"
}
```

**Blocked Command**

```json
{
  "type": "result",
  "decision": "block",
  "risk_score": 98,
  "rules_triggered": ["destructive_command", "sudo_used"],
  "command_id": "cmd_def456",
  "message": "Command blocked: potential destructive operation"
}
```

---

## MCP Endpoint

FlowLink acts as an MCP proxy:

```
URL: https://flowlink.flow-masters.ru/mcp
Auth: Bearer token in headers
```

Compatible with Claude Code, Cursor, Windsurf, and any MCP client.

---

## Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `unauthorized` | 401 | Missing or invalid API key |
| `forbidden` | 403 | Insufficient permissions |
| `not_found` | 404 | Resource not found |
| `rate_limited` | 429 | Too many requests |
| `agent_not_found` | 404 | Agent not connected |
| `command_blocked` | 403 | Command blocked by policy |
| `approval_required` | 202 | Command awaiting human approval |
