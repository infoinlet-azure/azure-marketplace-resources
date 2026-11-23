# Troubleshooting

[AI Cloud Gateway](README.md) > Troubleshooting

Every error code is also listed in the [API reference](api-reference.md#errors).

## Subscribing

| Problem | Fix |
|---|---|
| "This link has expired" | Select **Configure account** on the SaaS subscription in the Azure portal again |
| "This link is for a different product" | The link is for another offer. Open your AI Cloud Gateway subscription in the Azure portal and select **Configure account** |
| "Subscription already linked" | Another dashboard account linked it. Sign in with that one, or contact support |
| The plan says "Shown once Microsoft confirms it" | Wait a few minutes and reload. If it persists, contact support |
| `403 subscription_inactive` | The subscription is pending, suspended (usually a failed payment) or ended. Check **Subscriptions** and the Azure portal |

## Connecting your Foundry resource

| Message after **Check and save** | Fix |
|---|---|
| "AI Cloud Gateway Router isn't set up in tenant ..." | Complete step 1 on **Azure account**, or ask a tenant administrator to |
| "The approval didn't go through" | The administrator declined, or your tenant restricts app consent. Ask an administrator to approve it |
| "Microsoft Entra has no tenant ..." | Check the tenant ID |
| "AI Cloud Gateway Router is disabled in your tenant" | Ask an admin to enable its enterprise application |
| "This resource belongs to a different tenant ..." | Use the tenant of the Azure subscription that holds the resource |
| "AI Cloud Gateway Router can't read this resource" | Assign it the Foundry User role on the resource (step 2), wait a few minutes, check again |
| "There is no Foundry resource ..." | Copy the resource ID from the command on the page or the resource's JSON view |
| "This is a ... resource, not a Foundry resource" | Connect the Foundry (Azure AI Services) resource your models are deployed on |
| "This resource has no custom subdomain ..." | Set one on the resource's **Networking** page, then check again |
| "This resource has no model deployments yet" | [Deploy a model](models.md#deploy-a-model), then check again |
| "Waiting for Azure to apply the role" | Normal: Azure applies a new role to model calls minutes after the resource. The page checks again on its own |
| "Azure still hasn't applied the role" | Make sure the role is on the resource itself, not on a project inside it, then check again |
| "We couldn't get an answer from the models on ..." | Check that a chat model is deployed and that the resource accepts public network traffic |

## Calling the API

| Problem | Fix |
|---|---|
| `401 api_key_required` | Send `Authorization: Bearer acg_...` |
| `401 invalid_api_key` | The key is wrong or revoked. Create a new one on **API keys** |
| `403 foundry_not_connected` | [Connect your Foundry resource](getting-started.md#3-connect-your-foundry-resource) |
| `403 foundry_access_denied` | The router app lost the Foundry User role, or a new role is still applying. Check the role on the resource |
| `403 foundry_consent_missing` | The app was removed from your tenant. Approve it again on **Azure account** |
| `429 foundry_throttled` | Every deployment's rate limit is used up. Retry with backoff, or raise the deployments' capacity (50 or more) |
| `503 foundry_unavailable` or `foundry_token_unavailable` | A fault on our side. Retry shortly; contact support if it persists |
| Your client times out on a long answer | The gateway keeps the connection open, but your HTTP client may give up sooner. Raise its timeout, or use `stream: true` |
| `500` or `502` | Retry. If it persists, contact support |

## Models and routing

| Problem | Fix |
|---|---|
| `403 foundry_no_routable_deployments` | No catalog model is deployed and routed. [Deploy one](models.md#deploy-a-model), or include a deployment on **Azure account** |
| `403 model_not_permitted` | The pinned model is not in routing: deploy it, include its deployment, or switch it on on **Models**. If the message names another reason, the model cannot serve this request (for example it takes no images) |
| `400 foundry_deployment_not_found` | A deployment was removed. Select **Refresh** on **Models** |
| `404 model_not_found` | Use an id from `GET /models` |
| `400 no_eligible_model` | The input is too long, or needs images or tools no model in routing has. Trim it, or deploy or switch on more models |
| A deployment is never used | Its model must be in our [catalog](models.md#the-catalog): deployments listed as *Not in our catalog* are not routed. Otherwise check the model's status on **Models** |
| A model never answers `auto` requests | It must be *In routing*. Even then, `auto` picks it only when it is the cheapest model expected to answer well |
| A model shows *Test failed* | Hover the chip for the reason. [Common failures](models.md#test-a-model) |
| A new deployment is not used | Select **Refresh** on **Models** |
| A Claude deployment fails | Claude needs a paid pay-as-you-go Azure subscription and Anthropic's Marketplace terms, accepted on the first deployment |
| Saving a choice of models fails | A choice must leave at least one deployed model. Switch on a deployed model |
| Turns of one conversation answered by different models | Expected with `auto`. Pin a model to keep one |
| A tag had no effect (`x-acg-directives` missing) | Check the [tag rules](routing.md#tags) |
| `content` is empty or cut short, `finish_reason` is `length` | `max_tokens` is too small: hidden reasoning counts against it. Raise it to 8,000 or more for hard prompts, or leave it out. [Output cap and reasoning](api-reference.md#output-cap-and-reasoning) |
| `x-acg-warning: max_tokens_limits_thinking` | Your `max_tokens` made the gateway lower or switch off reasoning, or skip a model that reasons. Raise it for full-strength answers |

## Budgets and content filters

| Problem | Fix |
|---|---|
| `403 budget_exceeded` | A monthly limit on the subscription or the key is reached; the message says which. Raise or remove it on **Subscriptions** or **API keys**, or wait for 00:00 UTC on the 1st. [Budgets](controls.md#budgets) |
| Spend went past the spend budget | Expected: requests already in flight when spend reaches the limit complete. Request limits are exact |
| `503 budget_unavailable` | The gateway could not check a budget, so it sent the request to no model. Retry shortly; contact support if it persists |
| Estimated spend differs from the Azure bill | Expected: the estimate uses list prices. Your Azure bill is the final figure |
| `400 foundry_content_filter` | Your deployment's content filter refused the prompt. Review the filter in the Foundry portal |
| `finish_reason` is `content_filter` | The content filter, or the model itself, stopped the answer |

## Contact support

Email **contact@aicloudgateway.com** with your Azure Marketplace subscription ID
and, for a specific call, its `x-request-id` header (or the error code and
time). Never send your API key.
