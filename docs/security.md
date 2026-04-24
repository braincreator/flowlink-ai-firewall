# Security

## How FlowLink Protects Your Infrastructure

### Zero Trust Architecture

Every connection requires authentication:

- API key per agent (issued from dashboard)
- RBAC roles: admin, operator, viewer
- Device trust scoring (0-100)
- Session management with auto-cleanup
- 2FA support for admin actions

### Encryption

- E2EE: X25519 key exchange + AES-256-GCM encryption
- TLS 1.3 for all connections
- Encrypted audit log storage
- Secrets never stored in plaintext

### Audit Trail

Every command is logged with:

- Full command text
- Risk score and triggered rules
- Agent identity and source IP
- Timestamp and outcome (allowed/blocked/approved)
- Export to SIEM systems (CEF, LEEF, JSON)

### Least Privilege

- Agents can only execute explicitly allowed actions
- Per-agent policy configuration
- Time-based access restrictions
- Approval workflow for elevated operations

## Reporting Vulnerabilities

If you discover a security vulnerability in FlowLink, please report it responsibly:

- **Email:** security@flow-masters.ru
- **Telegram:** @braincreator89

We will respond within 48 hours and coordinate disclosure.

**Please do not** publicly disclose the vulnerability before we have resolved it.

## Security Features by Level

| Level | Protection | Bypass Difficulty |
|-------|-----------|-------------------|
| 1. Pattern Matching | Regex rules on command text | Easy (encoding) |
| 2. Risk Scoring | Context-aware scoring | Medium |
| 3. Policy Engine | Declarative access rules | Hard |
| 4. Approval Workflow | Human-in-the-loop | Very hard |
| 5. Pattern Learning | Cross-user threat detection | Very hard |
| 6. Shield (eBPF) | Kernel-level interception | Extremely hard |
| 7. MCP Gateway | Tool-level access control | Extremely hard |

## Compliance

FlowLink supports:

- Audit logging for SOC 2 requirements
- SIEM export for compliance reporting
- Role-based access control for regulatory needs
- Data retention policies per organization

## Incident Response

If FlowLink blocks a command that should be allowed:

1. Check the dashboard for the block reason
2. Review the risk score and triggered rules
3. Create an allow policy if the command is legitimate
4. Use approval workflow for one-time exceptions

If you suspect a security incident:

1. Review the full audit trail
2. Check agent connection logs
3. Export audit data for forensic analysis
4. Contact security@flow-masters.ru
