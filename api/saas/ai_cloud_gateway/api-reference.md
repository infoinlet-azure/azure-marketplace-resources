# API reference

[AI Cloud Gateway](README.md) > API reference

[Authentication](#authentication) | [POST /chat/completions](#post-chatcompletions) |
[Output cap and reasoning](#output-cap-and-reasoning) |
[GET /models](#get-models) | [Response headers](#response-headers) |
[Limits](#limits) | [Errors](#errors)

Base URL: `https://api.aicloudgateway.com/v1`. The API follows the OpenAI Chat
Completions format; fields that do not apply are ignored, not rejected.

## Authentication

```
Authorization: Bearer acg_...
```

Create keys on the dashboard's **API keys** page. A key is shown once and cannot
be recovered, only replaced. You can hold several, for example one per app.

**Rotate without downtime:** create a new key, deploy it, then revoke the old
one. A revoked key stops working within 60 seconds.

## POST /chat/completions

| Field | Type | Notes |
|---|---|---|
| `model` | string | `auto` (default), `auto:cost`, `auto:quality`, or a model id. See [routing](routing.md) and [pinning rules](models.md#pin-a-model) |
| `messages` | object[] | Required. Roles: `system`, `developer`, `user`, `assistant`, `tool` |
| `max_tokens` / `max_completion_tokens` | integer | Output cap. Includes hidden reasoning: see [output cap and reasoning](#output-cap-and-reasoning) |
| `temperature` | number | 0 to 2. Dropped for reasoning models (Claude 5.5 and Fable 5.2, GPT-5 and GPT-6), which accept only their default |
| `top_p` | number | 0 to 1. Dropped for the same models |
| `stop` | string or string[] | Stop sequences. Dropped for GPT-5 and GPT-6 models |
| `stream` | boolean | Server-sent events |
| `stream_options.include_usage` | boolean | Adds a final chunk with token usage |
| `tools` | object[] | Function tools with a JSON Schema `parameters` |
| `tool_choice` | string or object | `none`, `auto`, `required`, or `{"type": "function", "function": {"name": ...}}` |
| `response_format` | object | `{"type": "json_object"}` or `{"type": "json_schema", "json_schema": {"name": ..., "schema": {...}}}` |
| `n` | integer | Ignored: one choice is always returned |

Message `content` is a string or a list of parts: `{"type": "text", ...}` and
`{"type": "image_url", "image_url": {"url": "data:image/png;base64,..."}}`.
Send images as base64 data URLs (`png`, `jpeg`, `gif`, `webp`). They work with
every model that takes images.

**Response:**

```json
{
  "id": "chatcmpl-6f1c...",
  "object": "chat.completion",
  "created": 1760400000,
  "model": "openai.gpt-5-nano",
  "choices": [{
    "index": 0,
    "message": {"role": "assistant", "content": "A sports car is ..."},
    "finish_reason": "stop"
  }],
  "usage": {"prompt_tokens": 14, "completion_tokens": 52, "total_tokens": 66}
}
```

`model` is the model that answered. `finish_reason` is `stop`, `length`,
`tool_calls` or `content_filter`. Tool calls are in `message.tool_calls`.

`content_filter` means your deployment's
[content filter](controls.md#content-filters) or the model itself stopped the
answer. The status is still `200`.

**Streaming** returns `chat.completion.chunk` events ending with `data: [DONE]`.
If Foundry fails mid-stream, one event carries the [error](#errors), then
`[DONE]`.

**Non-streaming** answers can start with whitespace: the gateway sends a space
every 10 seconds while the model works, so the connection stays open. JSON
parsers, including the OpenAI SDKs, ignore it. If the model fails after that,
the status is still `200` and the body is the [error](#errors).

## Output cap and reasoning

Many models reason before they answer, even without an `effort:` tag. In the
recommended set these are GPT-5 nano, mini and full, and Claude Haiku, Sonnet
and Opus 5.5; others include Claude Fable 5.2, gpt-oss-120b and the GPT-6
models. The reasoning is not returned, but it counts
against `max_tokens` and is billed as output tokens (`usage.completion_tokens`
includes it). A hard prompt can use several thousand tokens of reasoning, and
under a small cap a model could spend the whole cap reasoning and return empty
`content` with `finish_reason: length`.

So when your `max_tokens` leaves too little room for the reasoning a request is
expected to need:

- With `auto`, models that cannot stop reasoning (such as Claude Opus 5.5 and
  the GPT-6 models) are skipped for one that can answer within the cap.
- GPT-5 models answer with minimal reasoning, and Claude Haiku 5.5 and Sonnet
  5.5 without reasoning (`x-acg-effort: none`). The answer comes first, but it
  is weaker on hard problems.
- A model you pinned that cannot stop reasoning reasons at low effort
  (`x-acg-effort: low`), and can still use up the cap.
- The response carries `x-acg-warning: max_tokens_limits_thinking`.

The gateway never raises your cap: it is your cost limit. For hard prompts, set
`max_tokens` to 8,000 or more, or leave it out (the model's own limit applies).
Easy prompts need little reasoning, so a small cap rarely changes them. With an
`effort:` tag, the cap applies to the answer, and the gateway adds room for the
reasoning that effort asks for.

## GET /models

Lists the routing modes (`auto`, `auto:cost`, `auto:balanced`, `auto:quality`)
and the ids of the models in routing: catalog models deployed on your Foundry
resource, not excluded, and switched on if you
[chose models](models.md#choose-models). With no catalog model deployed it
answers `403 foundry_no_routable_deployments`.

```bash
curl https://api.aicloudgateway.com/v1/models -H "Authorization: Bearer $ACG_API_KEY"
```

```json
{"object": "list", "data": [
  {"id": "auto", "object": "model", "created": 1760400000, "owned_by": "aicloudgateway"},
  {"id": "openai.gpt-5-nano", "object": "model", "created": 1760400000, "owned_by": "aicloudgateway"}
]}
```

## Response headers

| Header | Meaning |
|---|---|
| `x-request-id` | Request id. Quote it to support |
| `x-acg-model` | The model that answered |
| `x-acg-strategy` | `routed` (gateway chose) or `pinned` (you named the model) |
| `x-acg-mode` | `cost`, `balanced` or `quality` |
| `x-acg-catalog-version` | Model catalog version used for the choice |
| `x-acg-directives` | Tags applied, if any |
| `x-acg-effort` | Reasoning effort applied, if any: `low`, `medium`, `high`, or `none` when reasoning was switched off to fit `max_tokens` |
| `x-acg-warning` | `max_tokens_limits_thinking`: your `max_tokens` left too little room for reasoning. [Details](#output-cap-and-reasoning) |

## Limits

| Limit | Value |
|---|---|
| Time between bytes | 60 seconds. Non-streaming answers send a space every 10 seconds while the model works, so they do not reach it |
| Longest request | About 14 minutes. A longer answer ends with `finish_reason` `length` |
| Key and subscription changes | Apply within 60 seconds |
| Budget and model choice changes | Apply within about a minute |
| Deployment changes | Apply within about a minute of **Refresh** on **Models** |
| Foundry resources | Any Foundry resource with a custom subdomain and public network access. [Details](azure-account.md#which-resources-work) |

## Errors

OpenAI error envelope, so SDKs raise their usual exceptions. Match on `code`.

```json
{"error": {"message": "Connect your Microsoft Foundry resource on the dashboard's Azure account page before calling the API.",
           "type": "permission_error", "param": null, "code": "foundry_not_connected"}}
```

| Status | `code` | Fix |
|---|---|---|
| 400 | `invalid_request` | Fix the field named in `param` |
| 400 | `invalid_model` | Use `auto`, `auto:cost`, `auto:balanced` or `auto:quality` |
| 400 | `unsupported_image_url` | Send images as base64 data URLs (png, jpeg, gif, webp) |
| 400 | `invalid_tool_arguments` | Send tool-call `arguments` as valid JSON |
| 400 | `missing_tool_call_id` | Add `tool_call_id` to each `tool` message |
| 400 | `no_eligible_model` | Input too long, or needs images or tools no model in routing has. Trim it, or [deploy or switch on more models](models.md) |
| 400 | `foundry_validation_error` | Foundry rejected the request; the message says why |
| 400 | `foundry_content_filter` | Your deployment's [content filter](controls.md#content-filters) refused the prompt |
| 400 | `foundry_deployment_not_found` | A deployment was removed since the last check. Select **Refresh** on **Models** |
| 401 | `api_key_required` | Send `Authorization: Bearer acg_...` |
| 401 | `invalid_api_key` | Key wrong or revoked. Create a new one |
| 403 | `subscription_inactive` | Subscription pending, suspended or ended. Check **Subscriptions** and the Azure portal |
| 403 | `foundry_not_connected` | [Connect your Foundry resource](getting-started.md#3-connect-your-foundry-resource) |
| 403 | `foundry_no_routable_deployments` | No catalog model is deployed and routed. [Deploy one](models.md#deploy-a-model), or include a deployment on **Azure account** |
| 403 | `foundry_access_denied` | The router app lost the Foundry User role on the resource, or a new role is still applying (wait a few minutes) |
| 403 | `foundry_consent_missing` | *AI Cloud Gateway Router* was removed from your tenant. Approve it again on **Azure account** |
| 403 | `foundry_app_disabled` | The app is disabled in your tenant. Ask an admin to enable its enterprise application |
| 403 | `foundry_tenant_not_found` | Connect your Foundry resource again on **Azure account** |
| 403 | `model_not_permitted` | The pinned model cannot serve this request. It is not in routing (not deployed, excluded, or not switched on), or the request needs something it lacks, such as images, tools or a longer context. The message says which |
| 403 | `budget_exceeded` | A monthly [budget](controls.md#budgets) is reached. Raise it, or wait for the reset the message names. Not retried by SDKs |
| 404 | `model_not_found` | Use an id from `GET /models` |
| 429 | `foundry_throttled` | Retry with backoff, or raise your deployments' capacity |
| 500 | `internal_error` | Retry. Contact support if it persists |
| 502 | `foundry_error`, `foundry_timeout`, `foundry_unreachable` | Retry |
| 503 | `foundry_unavailable`, `foundry_token_unavailable` | A fault on our side. Retry shortly; contact support if it persists |
| 503 | `budget_unavailable` | A [budget](controls.md#budgets) could not be checked; the request was not sent to any model. Retry shortly |
