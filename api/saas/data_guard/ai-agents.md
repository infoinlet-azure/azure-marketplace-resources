# AI agents and MCP

[Data Guard API](README.md) > AI agents and MCP

Your subscription and API key also serve a hosted MCP server, for AI agents and
MCP clients such as Claude, VS Code and Copilot Studio. It is billed like the
REST API.

| | |
|---|---|
| Endpoint | `https://api.llmlinq.com/data-guard/mcp` |
| Transport | MCP streamable HTTP, stateless, JSON responses |
| Authentication | your API key: `Authorization: Bearer llq_live_...` or `X-Api-Key: llq_live_...` |

## Connect

Most clients take a configuration like this:

```json
{
  "mcpServers": {
    "data-guard": {
      "type": "http",
      "url": "https://api.llmlinq.com/data-guard/mcp",
      "headers": { "Authorization": "Bearer llq_live_..." }
    }
  }
}
```

Claude Code:

```bash
claude mcp add --transport http data-guard https://api.llmlinq.com/data-guard/mcp \
  --header "Authorization: Bearer $DG_API_KEY"
```

Keep the key out of shared configuration files; most clients can read it from
an environment variable.

## Tools

| Tool | Same as | What it does |
|---|---|---|
| `list_detectors` | `GET /detectors` | Entity types, packs, strategies. Free |
| `scan_data` | `POST /scan` | Find sensitive values, change nothing |
| `redact_data` | `POST /redact` | Redact under a policy, with a receipt |
| `restore_data` | `POST /restore` | Reverse an `encrypt` redaction |
| `check_policy` | `POST /check` | Pass or fail against a compliance pack |

Parameters and results are the same as the [REST API](api-reference.md), with
two rules:

- **Inline input only**: `text` or `base64_data`. A `path` is refused; the
  server cannot read files on your machine.
- **Pass `key` yourself** for the `hash` and `encrypt` strategies, `gdpr_basic`
  and `restore_data`. It is never stored.

A good instruction for the agent: call `scan_data` first, then `redact_data`
with the pack that matches your regime.

## Errors

A refused tool call (a bad parameter, a wrong restore key) comes back as a tool
error with the API's message. A key or subscription problem is an HTTP `401` or
`403` on the request, so your client shows it as an authentication failure.

Problems: [Troubleshooting](troubleshooting.md#ai-agents-and-mcp).
