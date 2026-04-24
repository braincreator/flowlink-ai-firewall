# Security Policy

## Reporting Vulnerabilities

If you discover a security vulnerability in FlowLink, please report it privately.

**Do NOT open a public GitHub issue for security vulnerabilities.**

### How to Report

- **Email:** [security@flow-masters.ru](mailto:security@flow-masters.ru)
- **Telegram:** [@braincreator89](https://t.me/braincreator89)

Please include:
- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Any suggested fix (optional)

### Response Timeline

| Action | Timeline |
|--------|----------|
| Acknowledge receipt | Within 24 hours |
| Initial assessment | Within 48 hours |
| Fix development | Within 7 days (critical: 48 hours) |
| Public disclosure | After fix is deployed |

## Supported Versions

| Version | Support Status |
|---------|---------------|
| FlowLink Cloud (latest) | ✅ Supported |
| FlowLink Agent (latest) | ✅ Supported |

## Scope

The following are in scope for security reports:

- Authentication and authorization bypasses
- Command injection that bypasses FlowLink's analysis
- Data exfiltration through unprotected channels
- API vulnerabilities (REST, WebSocket, MCP)
- Dashboard security issues
- Session management flaws

Out of scope:

- Social engineering attacks
- Physical access to servers
- Third-party service vulnerabilities (Cloudflare, Yandex, etc.)
- Issues in dependencies (report to upstream)

## Security Features

FlowLink itself implements:

- **E2EE** — X25519 + AES-256-GCM encryption for agent-relay communication
- **RBAC** — Role-based access control (admin, operator, viewer)
- **Device trust scoring** — 0-100 trust score per device
- **Killswitch** — Instant emergency stop per agent
- **Audit trail** — Every action logged with full context
- **Rate limiting** — Token bucket algorithm, 100 req/10s per IP
- **Session management** — Auto-cleanup of stale sessions
- **2FA** — TOTP-based two-factor authentication

## Hall of Fame

*Thank you to security researchers who have responsibly disclosed vulnerabilities.*

(No entries yet — be the first!)
