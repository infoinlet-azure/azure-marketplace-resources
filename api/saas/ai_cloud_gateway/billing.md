# Billing

[AI Cloud Gateway](README.md) > Billing

You pay for two things, both through Microsoft:

- **Routing**, to AI Cloud Gateway through Microsoft Marketplace: your plan,
  plus requests beyond its allowance. Shown on your Microsoft invoice.
- **Foundry model usage**, to Microsoft in the Azure subscription that holds
  your Foundry resource, at your prices. AI Cloud Gateway adds no markup and
  does not resell tokens.

## Plans

Monthly or annual. Prices are on the listing.

| Plan | Routed requests included per month of the term | Beyond the allowance, per 1,000 |
|---|---|---|
| Starter | 50,000 | $0.30 |
| Team | 1,000,000 | $0.25 |
| Business | 5,000,000 | $0.22 |

| Plan | Monthly | Annual |
|---|---|---|
| Starter | $19 | $190 |
| Team | $139 | $1,390 |
| Business | $649 | $6,490 |

Every plan routes the same way and covers one connected Foundry resource.

## The allowance

**Each plan includes its monthly figure for each month of your term**: an
annual Starter term includes 600,000 routed requests, usable at any point in
the year.

- The allowance follows your subscription term, which starts when the
  subscription is activated, and starts again when the term renews. Unused
  requests do not carry over.
- **Beyond the allowance, requests are billed per 1,000** at your plan's rate.
  Usage is reported to Microsoft hourly.
- The dashboard's **Usage** page shows the allowance used this term and anything
  beyond it.

## What counts

- **Only answered requests count.** Refused calls (`400`, `401`, `403`, `404`,
  or every model failing), including calls refused by a budget, and
  `GET /models` are free.
- An answer your content filter stopped (`finish_reason` `content_filter`) is an
  answered request, and counts.
- A **Test** on the **Models** page is not a routed request. Microsoft bills its
  few tokens to your Azure subscription, like any call to your deployment.

## Budgets and the allowance

They are different things:

- **The allowance** is how many routed requests your plan includes in the term.
  Going past it does not stop anything; the extra requests are billed per
  1,000.
- **A [budget](controls.md#budgets)** is an optional limit you set per calendar
  month (UTC). Once it is reached, requests are refused until the next month.
  It changes nothing in your plan or your allowance.

## Change or cancel

- **Upgrade** to a larger plan in the Azure portal during the term. Requests
  already used this term still count against it.
- **Cancel** in the Azure portal. The subscription ends, and if it was your
  only one, your API keys stop working.
  Cancellation and refunds follow Microsoft Marketplace's terms.
