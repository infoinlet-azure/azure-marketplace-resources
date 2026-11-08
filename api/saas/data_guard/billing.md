# Billing

[Data Guard API](README.md) > Billing

Everything is billed through Microsoft Marketplace and shown on your Microsoft
invoice. Prices are on the listing.

## What you pay

- **A flat fee**, monthly or annual.
- **Usage beyond the allowance**, on two dimensions:
  - **API calls**, per 1,000 successful calls.
  - **Data submitted**, per GB of request body.

## The allowance

**The fee includes 25,000 API calls and 1 GB for each month of your term**:
300,000 calls and 12 GB on an annual term, usable at any point in the year.

- The allowance follows your subscription term, which starts the day the
  subscription was activated, and resets when the term renews. Unused allowance
  does not carry over.
- Going past it does not stop anything; the extra usage is billed. It is
  reported to Microsoft hourly.
- The [configure page](https://cloud.llmlinq.com/azure/data-guard/configure)
  shows your usage.

## What counts

- **Only successful calls.** A `400`, `401`, `403`, `413` or `500` is free.
- `GET /detectors` is free.
- Over [MCP](ai-agents.md), a successful `scan_data`, `redact_data`,
  `restore_data` or `check_policy` is one API call. `list_detectors`,
  connecting and listing tools are free.
- Data submitted is the size of the request body, parameters included.

## Free trial

Not billed, and its usage is never reported to Microsoft. It allows 10,000 API
calls or 1 GB, whichever comes first, then answers `403 trial_limit_reached`
until it becomes a paid subscription. That happens on its own when the trial
ends, with the same subscription and API key, unless you cancel first.

## Change or cancel

Cancellation and refunds follow Microsoft Marketplace's terms and are managed in
the Azure portal.
