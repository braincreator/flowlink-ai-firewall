# Comparison

## FlowLink vs Alternatives

AI coding agents execute shell commands on your servers. Different approaches exist to mitigate the risk:

| Approach | How It Works | Catches |
|----------|-------------|---------|
| **Runtime Firewall** (FlowLink) | Intercepts commands at kernel level before execution | Shell injection, credential theft, data exfiltration |
| **Prompt Scanners** | Analyzes AI prompts for malicious intent | Known attack patterns in prompts |
| **LLM Proxies** | Filters LLM input/output for unsafe content | Unsafe completions, prompt injection |
| **Manual RBAC** | Human-configured access controls | Authorized actions only |

## Feature Comparison

| Feature | FlowLink | Prompt Scanners | LLM Proxies | Manual RBAC |
|---------|----------|----------------|-------------|-------------|
| Runtime interception | Kernel-level (eBPF) | Text-only | Text-only | Manual |
| Shell injection | Blocked at exec | Heuristic | Not checked | Manual |
| Credential theft | Blocked | Not checked | Not checked | Manual |
| Data exfiltration | Blocked | Not checked | Not checked | Manual |
| MCP support | Native proxy | Not checked | Not checked | Manual |
| Approval workflow | Built-in | Not available | Not available | Manual |
| Latency | < 1ms | < 1ms | 100ms+ | < 1ms |
| Code changes needed | None | None | Modify app | Manual setup |
| Zero trust | Full | None | Basic | Basic |
| Audit trail | Full | Partial | Partial | Manual |
| Pattern learning | Cross-user | None | None | None |
| SIEM export | CEF/LEEF/JSON | None | None | None |

## Why Runtime > Text-Only

### The Gap in Prompt Scanners

Prompt scanners analyze what the AI *says* it will do. But AI can:

1. **Decompose** a malicious command into safe-looking parts
2. **Encode** commands in base64 or hex
3. **Use indirect execution** (`eval`, `bash -c`)
4. **Chain** multiple safe commands into a dangerous sequence
5. **Exploit tools** (file write + cron = persistence)

FlowLink catches all of these because it analyzes the **actual system call**, not the prompt.

### The Gap in LLM Proxies

LLM proxies filter the AI model's input and output. They cannot:

1. **See** what commands the agent actually executes
2. **Prevent** direct tool use (MCP, function calling)
3. **Protect** against agent autonomy (tool selection)
4. **Control** filesystem operations

FlowLink operates at the execution layer, independent of the AI model.

## Use Cases

| Use Case | Best Tool | Why |
|----------|-----------|-----|
| Protect production servers | **FlowLink** | Runtime interception, kernel-level |
| Filter AI responses in chat | LLM Proxy | Output filtering |
| Basic prompt safety | Prompt Scanner | Lightweight, no agent changes |
| Enterprise compliance | **FlowLink** | Audit trail, SIEM export, RBAC |
| Development environment | Prompt Scanner | Quick protection, no infrastructure |
| Multi-agent orchestration | **FlowLink** | MCP proxy, per-agent policies |

## Security Layers (Defense in Depth)

FlowLink is designed to complement, not replace, other security tools:

```
Layer 1: Prompt Scanner    — first line of defense
Layer 2: LLM Proxy         — output filtering
Layer 3: FlowLink          — runtime enforcement (last line)
```

Even if an attack bypasses layers 1 and 2, FlowLink catches it at execution time.
