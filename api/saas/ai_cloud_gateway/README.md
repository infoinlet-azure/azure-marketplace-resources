# AI Cloud Gateway

An LLM router for Microsoft Foundry. Each chat request goes to the lowest-cost
model deployment on **your own Foundry resource** that is expected to answer it
well. Microsoft bills the model usage to your Azure subscription at your prices,
and AI Cloud Gateway bills only for routing.

The choice is **deterministic**: a rules engine, not another model, picks in
milliseconds, and the same request always gets the same model.

It speaks the OpenAI Chat Completions format, so your existing SDK or agent
framework works by changing two settings:

- Base URL: `https://api.aicloudgateway.com/v1`
- API key: `acg_...` from the [dashboard](https://dashboard.aicloudgateway.com)

Subscribe to **AI Cloud Gateway - LLM Router for Microsoft Foundry** in
Microsoft Marketplace.

The service itself is hosted on AWS in `us-east-1`: requests pass through it in
memory on their way to your Foundry resource. See [Data handling](data-handling.md).

## Guides

**[Getting started](getting-started.md)** - **Start here.** Subscribe, create a
key, connect your Foundry resource and make your first call.

**[Examples](examples.md)** - Code to copy: Python, JavaScript, LangChain,
streaming, tool calls, JSON output and images.

**[Troubleshooting](troubleshooting.md)** - Common problems by stage, their fix,
and how to reach support.

**[How routing works](routing.md)** - How a model is chosen, when to use
`auto:cost` or `auto:quality` or pin a model, and tags that steer the choice.

**[Models](models.md)** - Tiers, the recommended set, deploying models on your
resource, choosing and testing them, pinning rules, and the model catalog.

**[Budgets and content filters](controls.md)** - Monthly limits on spend or
requests, per subscription or API key, and how your deployments' content
filters apply.

**[Your Azure account](azure-account.md)** - What the router app can and cannot
do on your Foundry resource, which deployments it routes to, and disconnecting.

**[API reference](api-reference.md)** - Request fields, responses, headers,
limits, key rotation and every error code.

**[Billing](billing.md)** - Plans, the allowance per term, and what counts as a
billed request.

**[Data handling](data-handling.md)** - What is stored and what is not, for
security and compliance reviews.

Full documentation: [aicloudgateway.com/docs](https://aicloudgateway.com/docs/).

## Support

**contact@aicloudgateway.com** - we reply within one business day, Monday to Friday.
