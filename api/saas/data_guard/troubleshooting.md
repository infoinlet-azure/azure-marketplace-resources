# Troubleshooting

[Data Guard API](README.md) > Troubleshooting

Every error code is also in the [API reference](api-reference.md#errors).

## Subscribing

| Problem | Fix |
|---|---|
| "This link has expired" | The setup link lasts an hour. Select **Configure account** in the Azure portal again |
| "We couldn't identify this purchase" | Reopen the SaaS subscription in the Azure portal or Microsoft 365 admin center and select **Configure account** again |
| "Your organization requires an admin to approve LLMLinq" | Your tenant restricts app consent. Ask your IT admin to approve LLMLinq, or sign in with an LLMLinq email and password |
| "An LLMLinq account already uses this email" | Sign in with that account's email and password, then attach the subscription |
| "That email address can't be used to create an LLMLinq account" | Start again from **Configure account** in the Azure portal, which allows a work address, or use a personal address |
| Attach fails, naming another account | The subscription is attached to a different account. Sign in as that account |
| Lost the API key | Issue a new one on the [configure page](https://cloud.llmlinq.com/azure/data-guard/configure). It cannot be recovered, only replaced |

## Calling the API

| Problem | Fix |
|---|---|
| `401 api_key_required` | Send `Authorization: Bearer llq_live_...` or `X-Api-Key` |
| `401 invalid_api_key` | The key was replaced or mistyped. Issue a new one on the configure page |
| `403 subscription_inactive` | Ended, suspended (usually a failed payment) or not yet activated. Check the subscription in the Azure portal |
| `403 trial_limit_reached` | The trial is used up. Calls resume once it becomes paid; the key stays the same |
| `400 unsupported_parameters` naming `path` | Files are not read over HTTP. Send the content as `text` or `base64_data` |
| `400` "Input too large" | Over 2 MiB. Split the document |
| `413 result_too_large` | The result is over 256 KiB. Split the document |
| `429` | Over the rate limit. Retry with exponential backoff |
| `500` | Retry. If it persists, contact support |

## Redaction

| Problem | Fix |
|---|---|
| `400` "The 'hash' strategy requires a key" | Pass `key`. `gdpr_basic` uses `hash`, so it needs one too |
| `400` "No encrypted placeholders were found" | `/restore` only reverses `encrypt` output (`<US_SSN:enc:...>`) |
| `400` "Could not restore ... no changes were applied" | Wrong key, or a placeholder was changed |
| Fewer findings than expected in JSON or CSV | It failed to parse and was scanned as text. Check `document_kind`; pass `kind` |
| A person's name was not redacted | Names with no title, label or field name are not detected. Pass it in `extra_spans` |
| Too many findings | Use `entity_types`, raise `min_confidence`, or pick a narrower pack. `strict_all` over-redacts on purpose |
| `document_truncated: true` | The document was only partly examined. Split it |

## AI agents and MCP

| Problem | Fix |
|---|---|
| MCP client cannot connect, or reports an auth error | Check the header on `https://api.llmlinq.com/data-guard/mcp`: `Authorization: Bearer llq_live_...`. A `403` means the subscription is not active |
| MCP tool error naming `path` | The hosted server takes `text` or `base64_data` only |

## Contact support

Email **contact@infoinlet.com** with the SaaS subscription ID from the Azure
portal and, for a specific call, its `request_id` (or the error code and time).
We cannot see your documents, so that is how we trace a call. Never send your
API key, redaction keys or real sensitive values.
