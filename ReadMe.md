# Microsoft Marketplace Resources

Supplementary resources and guides for our **Microsoft Marketplace products**.
These materials help subscribers set up, integrate, and operate our offerings.

The repository covers one delivery type today:

- **API products (SaaS)** - hosted HTTPS APIs you call with an API key, with
  nothing to deploy.

---

## Repository structure

```
azure-marketplace-resources/
└── api/                                  # API-based Marketplace products
    └── saas/                             # SaaS delivery, hosted by us
        ├── ai_cloud_gateway/             # Guide set: start at README.md
        │   ├── README.md                 # Index: what each page is for
        │   ├── getting-started.md        # Subscribe to first call
        │   ├── models.md                 # Deploy, choose and test models; the model catalog
        │   ├── azure-account.md          # The router app, your Foundry resource, deployments
        │   └── ...                       # Routing, examples, API reference, billing, data
        └── data_guard/                   # Guide set: start at README.md
            ├── README.md                 # Index: what each page is for
            ├── getting-started.md        # Subscribe to first call
            └── ...                       # Examples, AI agents, policies, API reference, billing
```

---

## Products

### API products (SaaS)

| Product | Description | Resources |
|---|---|---|
| Data Guard API | HTTPS API that detects and redacts PII, PHI, financial data, and secrets across text, JSON, NDJSON, YAML, and CSV, with compliance policy packs and auditable receipts. Includes a hosted MCP server for AI agents. | [Getting started](api/saas/data_guard/getting-started.md) · [All docs](api/saas/data_guard/README.md) |
| AI Cloud Gateway | HTTPS API that routes each chat request to a suitable model deployed on your own Microsoft Foundry resource, billed per routed request. You choose the models it may use, from OpenAI, Anthropic, Microsoft, Meta, Mistral and other providers. | [Getting started](api/saas/ai_cloud_gateway/getting-started.md) · [All docs](api/saas/ai_cloud_gateway/README.md) |

---

## Prerequisites

- An active subscription to the corresponding product in Microsoft Marketplace.
- A Microsoft work or personal account to sign in with.
- SaaS API products need only the API key you create after subscribing, and an
  HTTPS client. AI Cloud Gateway also needs a Microsoft Foundry resource in your
  Azure subscription, with at least one model deployed.

---

## Getting started

1. Clone this repository:
   ```bash
   git clone https://github.com/infoinlet-azure/azure-marketplace-resources.git
   cd azure-marketplace-resources
   ```
2. Open the guide for your product:
   - Data Guard API (SaaS): [`api/saas/data_guard/getting-started.md`](api/saas/data_guard/getting-started.md)
   - AI Cloud Gateway (SaaS): [`api/saas/ai_cloud_gateway/getting-started.md`](api/saas/ai_cloud_gateway/getting-started.md)
3. Follow that guide for setup and integration.

---

## Support

- Product-specific questions and subscription issues: use the support link on
  the product's Microsoft Marketplace listing, or email:
  - Data Guard: **contact@infoinlet.com**
  - AI Cloud Gateway: **contact@aicloudgateway.com**
- Problems with the materials in this repository: open a GitHub issue.

When reporting an issue, never include API keys, credentials, or other
sensitive information.

---

## Notes

- Microsoft Marketplace listing pages are the authoritative source for each
  product's current features, pricing, and requirements.
