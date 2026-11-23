# Models

[AI Cloud Gateway](README.md) > Models

The gateway routes among the models in its Microsoft Foundry catalog: models
from OpenAI, Anthropic, Microsoft, DeepSeek, Meta, Mistral, Moonshot AI and
xAI. A model is used only once it is **deployed** on your Foundry resource. You
see and manage them on the dashboard's **Models** page.

[Tiers](#tiers) | [Recommended models](#recommended-models) |
[Deploy a model](#deploy-a-model) | [Choose models](#choose-models) |
[Status](#status) | [Test a model](#test-a-model) | [Pin a model](#pin-a-model) |
[New models](#new-models) | [List your models](#list-your-models) |
[The catalog](#the-catalog)

## Tiers

Every model has one tier, set by us. It says what the model is for, not what it
costs. You cannot change a model's tier.

| Tier | For |
|---|---|
| **Light** | Greetings, classification, short extraction |
| **Standard** | Everyday writing, summaries, ordinary code |
| **Advanced** | Proofs, hard code, multi-step reasoning |

The Models page shows one column per tier, with how many of its models are in
routing. *None in routing* means requests of that tier go to models of another
tier. Within the models in routing, the gateway still picks the cheapest one
expected to answer each request well.

## Recommended models

About two models per tier are **recommended**, marked with a star. On Foundry
they are the models we suggest deploying:

`openai.gpt-5-nano`, `openai.gpt-5-mini`, `openai.gpt-5`,
`anthropic.claude-haiku-5-5`, `anthropic.claude-sonnet-5-5`,
`anthropic.claude-opus-5-5`.

Routing does not depend on the star. Until you choose models, it uses every
catalog model deployed on your resource.

## Deploy a model

The router app has only the Foundry User role, so it cannot deploy for you.

1. On **Models**, select **Deploy** on a model marked *Not deployed*.
2. Run the command shown with the Azure CLI, or deploy the same model from the
   [Foundry portal](https://ai.azure.com). The command is filled in for your
   resource, for example:

   ```bash
   az cognitiveservices account deployment create \
     --subscription <subscription id> -g <resource group> -n <resource> \
     --deployment-name gpt-5-mini --model-name gpt-5-mini \
     --model-version 2025-08-07 --model-format OpenAI \
     --sku-name GlobalStandard --sku-capacity 50
   ```

3. Select **Refresh deployments** in the dialog, or **Refresh** at the top of
   the card. The model is now *In routing*, if it is switched on.

Notes:

- **Capacity.** On Global Standard, a capacity of 50 is 50,000 tokens and 50
  requests a minute. A capacity of 1 allows 1 request a minute, so use 50 or
  more.
- **Claude** needs a paid pay-as-you-go Azure subscription, and Anthropic's
  Marketplace terms, which Azure asks you to accept on the first deployment.
- The deployment name can be anything. The router matches on the model name.
  [How deployments are matched](azure-account.md#which-deployments-are-routed).
- Microsoft bills each deployment's usage to your Azure subscription.

## Choose models

By default, every catalog model deployed on your resource is in routing. To
narrow that:

1. On **Models**, select **Choose models**. Each model becomes a switch.
2. Switch models on or off. The provider buttons pick the deployed models of
   whole providers. The chips and column counts preview routing as you go.
3. Select **Save**. It takes effect within a minute.

Once saved, routing uses exactly the models you switched on that are deployed.
You can switch on a model that is not deployed yet; it routes once it is.
**Reset to recommended** clears your choice, so every deployed catalog model is
in routing again. A choice that leaves no deployed model is refused.

Excluding a deployment on **Azure account** also keeps it out of routing.
[Which deployments are routed](azure-account.md#which-deployments-are-routed).

## Status

Each model shows one status:

| Status | Meaning |
|---|---|
| *In routing* | Switched on and deployed. A tick badge means it passed its last test. Hover to see the deployment names |
| *Not deployed* | Switched on, but not deployed on your resource. [Deploy it](#deploy-a-model) |
| *Excluded* | Deployed, but every deployment of it is excluded on **Azure account** |
| *Off* | Not in your routing |
| *Test failed* | Its last [test](#test-a-model) failed. Hover for the reason |

Deployments of models not in our catalog are listed below the columns as *Not in
our catalog*. They are not routed.

## Test a model

**Test** sends one tiny prompt (at most 16 output tokens) to the model's
deployment as the router app, through the same API the router uses. A pass marks
the model as tested. A failure says what to do:

| Failure | Fix |
|---|---|
| The router app may not call this deployment | The role was assigned in the last few minutes: wait, then test again. For Claude, accept the Marketplace terms when deploying it in the Foundry portal |
| No deployment with that name any more | Select **Refresh** |
| `429`: the rate limit is used up | Raise the deployment's capacity to 50 or more |
| Azure refused the request | Read Azure's message. The model may not be offered in your resource's region |

Microsoft bills the test as a normal small request. It is not a routed request,
so it does not count toward your plan.

## Pin a model

Set `model` to a catalog id to use one fixed model. See
[How routing works](routing.md#pick-a-mode).

- You can pin only a model that is in routing: deployed, not excluded, and
  switched on if you chose models. Any other pin gets `403 model_not_permitted`.
- Fallback stays within the models in routing.

## New models

When we add models to the catalog, they arrive **not recommended**, and a
catalog update never changes your routing by itself. If you have chosen models,
new ones show a *New* badge and stay off until you switch them on. If you have
not chosen, a new model is routed once you deploy it. The response header
`x-acg-catalog-version` names the catalog used for each request.

## List your models

```bash
curl https://api.aicloudgateway.com/v1/models -H "Authorization: Bearer $ACG_API_KEY"
```

It lists the routing modes (`auto`, `auto:cost`, `auto:balanced`,
`auto:quality`) and the ids of the models in routing.

## The catalog

Catalog `foundry-2026-11-23`. **Foundry model name** is the model to deploy; the
**Deploy** button fills in the format and version below. Context and maximum
output are in tokens. Whether a model is offered depends on your resource's
region. The **Models** page shows token prices; your Azure bill is the final
figure.

<!-- Generated from catalog foundry-2026-11-23. Do not edit by hand. -->

| Model id | Provider | Tier | Recommended | Foundry model name | Format, version | Context / max output | Supports |
|---|---|---|---|---|---|---|---|
| `openai.gpt-5-nano` | OpenAI | Light | Yes | `gpt-5-nano` | OpenAI, `2025-08-07` | 400K / 128K | tools, images, JSON |
| `microsoft.phi-4` | Microsoft | Light |  | `Phi-4` | Microsoft, `7` | 16K / 4K | JSON |
| `microsoft.phi-4-mini` | Microsoft | Light |  | `Phi-4-mini-instruct` | Microsoft, `1` | 128K / 4K | JSON |
| `openai.gpt-4.1-nano` | OpenAI | Light |  | `gpt-4.1-nano` | OpenAI, `2025-04-14` | 1M / 32K | tools, images, JSON |
| `openai.gpt-6-luna` | OpenAI | Light |  | `gpt-6-luna` | OpenAI, `2026-09-22` | 200K / 32K | tools, JSON |
| `anthropic.claude-haiku-5-5` | Anthropic | Standard | Yes | `claude-haiku-5-5` | Anthropic, `2` | 1M / 128K | tools, images, JSON |
| `openai.gpt-5-mini` | OpenAI | Standard | Yes | `gpt-5-mini` | OpenAI, `2025-08-07` | 400K / 128K | tools, images, JSON |
| `deepseek.v3-2` | DeepSeek | Standard |  | `DeepSeek-V3.2` | DeepSeek, `1` | 128K / 16K | tools, JSON |
| `deepseek.v4-flash` | DeepSeek | Standard |  | `DeepSeek-V4-Flash` | DeepSeek, `2026-04-23` | 128K / 16K | tools, JSON |
| `meta.llama-3-3-70b` | Meta | Standard |  | `Llama-3.3-70B-Instruct` | Meta, `9` | 128K / 8K | JSON |
| `meta.llama-4-maverick` | Meta | Standard |  | `Llama-4-Maverick-17B-128E-Instruct-FP8` | Meta, `1` | 128K / 8K | images, JSON |
| `mistral.mistral-large-3` | Mistral | Standard |  | `Mistral-Large-3` | Mistral AI, `1` | 128K / 16K | tools, images, JSON |
| `openai.gpt-4.1` | OpenAI | Standard |  | `gpt-4.1` | OpenAI, `2025-04-14` | 1M / 32K | tools, images, JSON |
| `openai.gpt-4.1-mini` | OpenAI | Standard |  | `gpt-4.1-mini` | OpenAI, `2025-04-14` | 1M / 32K | tools, images, JSON |
| `openai.gpt-6-1-terra` | OpenAI | Standard |  | `gpt-6.1-terra` | OpenAI, `2026-10-26` | 200K / 32K | tools, JSON |
| `openai.gpt-oss-120b` | OpenAI | Standard |  | `gpt-oss-120b` | OpenAI-OSS, `1` | 128K / 32K | tools, JSON |
| `anthropic.claude-opus-5-5` | Anthropic | Advanced | Yes | `claude-opus-5-5` | Anthropic, `2` | 1M / 128K | tools, images, JSON |
| `anthropic.claude-sonnet-5-5` | Anthropic | Advanced | Yes | `claude-sonnet-5-5` | Anthropic, `2` | 1M / 128K | tools, images, JSON |
| `openai.gpt-5` | OpenAI | Advanced | Yes | `gpt-5` | OpenAI, `2025-08-07` | 400K / 128K | tools, images, JSON |
| `anthropic.claude-fable-5-2` | Anthropic | Advanced |  | `claude-fable-5-2` | Anthropic, `1` | 1M / 128K | tools, images, JSON |
| `deepseek.v4-pro` | DeepSeek | Advanced |  | `DeepSeek-V4-Pro` | DeepSeek, `2026-04-23` | 128K / 16K | tools, JSON |
| `moonshotai.kimi-k3-1-pro` | Moonshot AI | Advanced |  | `Kimi-K3.1-Pro` | MoonshotAI, `2026-11-04` | 128K / 16K | tools, JSON |
| `openai.gpt-6-2-astra` | OpenAI | Advanced |  | `gpt-6.2-astra` | OpenAI, `2026-11-17` | 200K / 32K | tools, JSON |
| `openai.gpt-6-2-sol` | OpenAI | Advanced |  | `gpt-6.2-sol` | OpenAI, `2026-11-17` | 200K / 32K | tools, JSON |
| `xai.grok-4-7` | xAI | Advanced |  | `grok-4.7` | xAI, `1` | 128K / 16K | tools, JSON |

<!-- End of generated table (25 models). -->
