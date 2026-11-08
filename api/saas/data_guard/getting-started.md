# Getting started

[Data Guard API](README.md) > Getting started

Three steps, about 5 minutes. You need a Microsoft work or personal account (or
an existing LLMLinq email and password) and an HTTPS client such as `curl`. No
SDK. Buying from the Azure portal needs an Azure subscription and permission to
purchase in it.

## 1. Subscribe

Subscribe to **Data Guard - PII, PHI, and Secret Redaction API** in Microsoft
Marketplace and pick a plan and term. Some plans include a
[free trial](#free-trial).

## 2. Activate and create a key

1. Open the new SaaS subscription in the Azure portal and select **Configure
   account**. You are sent to `cloud.llmlinq.com/azure/data-guard/landing`.
2. Select **Continue with Microsoft**, or sign in with an LLMLinq email and
   password.
3. Check the account the subscription will be attached to, then select
   **Attach and activate**. Billing starts here. A subscription can be attached
   to only one account.
4. Wait for Microsoft to confirm, usually under a minute. The page updates on
   its own.
5. Select **Create API key** and **copy it now**. It starts with `llq_live_` and
   is **shown once**. Store it in a secret manager.

Your key, subscription state and usage stay at
[cloud.llmlinq.com/azure/data-guard/configure](https://cloud.llmlinq.com/azure/data-guard/configure).
Lost the key? Select **Issue a new key** there; the old one stops working
immediately. One key covers every Data Guard subscription on your account.

## 3. Make your first call

```bash
export DG_API_KEY="llq_live_..."

curl -X POST https://api.llmlinq.com/data-guard/v1/redact \
  -H "Authorization: Bearer $DG_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"text": "Call Sarah on 415-555-2671, SSN 123-45-6789"}'
```

```json
{
  "text": "Call Sarah on <PHONE_NUMBER>, SSN <US_SSN>",
  "receipt": {"total_redactions": 2, "by_entity_type": {"PHONE_NUMBER": 1, "US_SSN": 1}, "...": "..."},
  "...": "..."
}
```

Use `/scan` instead of `/redact` to see what is there without changing
anything. Each finding says **where** a value is, never what it is.

If you get an error instead:

| `error` | Go back to |
|---|---|
| `api_key_required`, `invalid_api_key` | Step 2: check the header and the key |
| `subscription_inactive` | Step 2: finish activation, or check the subscription in the Azure portal |

Anything else: [Troubleshooting](troubleshooting.md).

## Free trial

If your plan offers one, the steps above are the same.

- **No charge** for the fee or for usage during the trial.
- **Allowance:** 10,000 API calls or 1 GB of request data, whichever comes
  first. The configure page shows how much is used. Past it, calls return
  `403 trial_limit_reached`; `GET /detectors` keeps working.
- **It becomes a paid subscription on its own** when the trial ends, with the
  same API key, unless you cancel it first in the Azure portal.

## Next

- [Examples](examples.md) - redact JSON and CSV, gate on a compliance pack,
  reversible redaction.
- [AI agents and MCP](ai-agents.md) - use it from Claude, VS Code or Copilot
  Studio.
- [Policies and strategies](policies.md) - choose a pack for your regime.
