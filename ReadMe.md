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
        └── data_guard.md                 # Usage guide
```

---

## Products

### API products (SaaS)

| Product | Description | Resources |
|---|---|---|
| Data Guard API | HTTPS API that detects and redacts PII, PHI, financial data, and secrets across text, JSON, NDJSON, YAML, and CSV, with compliance policy packs and auditable receipts. | [Usage guide](api/saas/data_guard.md) |

---

## Prerequisites

- An active subscription to the corresponding product in Microsoft Marketplace.
- A Microsoft work or personal account to sign in with.
- SaaS API products need only the API key you create after subscribing, and an
  HTTPS client.

---

## Getting started

1. Clone this repository:
   ```bash
   git clone https://github.com/infoinlet-azure/azure-marketplace-resources.git
   cd azure-marketplace-resources
   ```
2. Open the guide for your product:
   - Data Guard API (SaaS): [`api/saas/data_guard.md`](api/saas/data_guard.md)
3. Follow that guide for setup and integration.

---

## Support

- Product-specific questions and subscription issues: email
  **contact@infoinlet.com**, or use the support link on the product's Microsoft
  Marketplace listing.
- Problems with the materials in this repository: open a GitHub issue.

When reporting an issue, never include API keys, credentials, or other
sensitive information.

---

## Notes

- Microsoft Marketplace listing pages are the authoritative source for each
  product's current features, pricing, and requirements.
