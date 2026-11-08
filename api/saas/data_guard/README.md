# Data Guard API

Finds and redacts PII, PHI, financial data and secrets in text, JSON, NDJSON,
YAML and CSV, before they reach a log, a third party or a model's context.

- **Hosted**: nothing to deploy. Subscribe, create an API key, call the API.
- **Deterministic**: checksums, patterns, field-name rules and word lists, no ML
  model. The same input always gives the same result, and every finding names
  the rule behind it.
- **Never echoes what it protects**: findings carry offsets and a masked
  preview, never the value. Nothing about a request is stored.
- **64 entity types**, six compliance policy packs, seven redaction strategies.

Two ways to call it, with one API key and one subscription:

- REST API: `https://api.llmlinq.com/data-guard/v1`
- MCP server for AI agents: `https://api.llmlinq.com/data-guard/mcp`

## Guides

**[Getting started](getting-started.md)** - **Start here.** Subscribe, create
your key and make your first call, in about 5 minutes. Includes the free trial.

**[Examples](examples.md)** - Code to copy: scan, redact, structured documents,
compliance gates, reversible redaction, joinable pseudonyms.

**[AI agents and MCP](ai-agents.md)** - Connect Claude, VS Code, Copilot Studio
or any MCP client to the hosted MCP server.

**[Policies and strategies](policies.md)** - Which pack to pick, what each
redaction strategy leaves behind, and handling keys.

**[API reference](api-reference.md)** - Endpoints, parameters, responses,
errors and limits.

**[Billing](billing.md)** - Plans, the allowance per term, usage, free trial.

**[Troubleshooting](troubleshooting.md)** - Common problems and their fix.

**[Data handling and limits](data-handling.md)** - What is stored, what the
service does and does not claim, and known detection limits. For security and
compliance reviews.

Public reference, no key needed:
[interactive docs](https://api.llmlinq.com/data-guard/v1/docs) ·
[OpenAPI 3.1](https://api.llmlinq.com/data-guard/v1/openapi.yaml)

## Support

**contact@infoinlet.com** - we reply within one business day, Monday to Friday.
