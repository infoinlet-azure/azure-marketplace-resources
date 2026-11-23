# Budgets and content filters

[AI Cloud Gateway](README.md) > Budgets and content filters

Budgets are optional and off by default. Changes take effect within about a
minute.

[Budgets](#budgets) | [Content filters](#content-filters)

## Budgets

A budget stops requests once a monthly limit is reached. You can set two
limits, alone or together:

- **Monthly spend limit (USD)**: estimated Foundry model spend.
- **Monthly request limit**: answered requests.

### Set a budget

- **For the whole subscription:** on **Subscriptions**, in the **Monthly
  budget** card.
- **For one API key:** on **API keys**, select **Budget** on the key's row.

Enter a limit and select **Save limits**. Leave a field empty for no limit;
**Remove limits** clears both. Spend takes dollars and cents, requests a whole
number.

The subscription's budget and the key's budget both apply. The first limit
reached stops the request. Each budget shows this month's use against its
limits and the reset date.

### What counts

- Every answered request counts, whether or not a budget is set, so a budget
  set mid-month starts from the real month-to-date figure.
- **Spend is an estimate:** the request's tokens at the list price in our
  Foundry catalog for the model that answered (Global Standard prices). It does
  not reflect prompt caching, discounts, Data Zone or provisioned deployments,
  or your Azure agreement. Your Azure bill is the final figure.
- Refused requests reach no model and do not count.

### When a limit is reached

Requests get `403 budget_exceeded`. The message names the limit and when
requests are accepted again:

```json
{"error": {"message": "This API key has reached its monthly budget of 1,000 requests. Requests are accepted again from 2026-12-01 00:00 UTC, or as soon as the budget is raised or removed in the dashboard.",
           "type": "permission_error", "param": null, "code": "budget_exceeded"}}
```

It is a `403`, not a `429`, so SDKs do not retry it.

- **A request limit is exact.** Each request takes its place under the limit
  before it is routed, so however many run at once, no more than the limit are
  answered. A request that gets no answer gives its place back.
- **A spend limit can go slightly past.** A request's cost is known only after
  it is answered, so requests already in flight when spend reaches the limit
  complete. Nothing is cut off mid-answer.
- **If the gateway cannot check a budget, it refuses.** The request gets
  `503 budget_unavailable` and is not sent to any model. Retry it shortly.
- **The month is the calendar month in UTC.** Requests are accepted again at
  00:00 UTC on the first of the next month, or within about a minute of raising
  or removing the limit.

A budget is separate from your plan's allowance. See
[Billing](billing.md#budgets-and-the-allowance).

## Content filters

The gateway has no guardrail setting of its own on Azure. Your deployments'
content filters, set on your Foundry resource, apply to every routed request as
they apply to any call. Change them in the Foundry portal.

- **A prompt the filter refuses** gets `400 foundry_content_filter` with
  Azure's message. It is not retried on another model, because the same filter
  would most likely refuse it there too.
- **An answer the filter stops** returns `200` with `finish_reason`
  `content_filter`. It counts as a routed request, and its tokens count toward
  your budget.
- **Streaming:** if the filter stops a stream that has started, one event
  carries the `foundry_content_filter` error, then `[DONE]`.

Different deployments can have different filters, so the same prompt can pass
on one model and be refused on another.
