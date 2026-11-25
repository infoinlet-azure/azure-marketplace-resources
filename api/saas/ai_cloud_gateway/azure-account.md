# Your Azure account

[AI Cloud Gateway](README.md) > Your Azure account

Models run on your own Microsoft Foundry resource, called by our app *AI Cloud
Gateway Router* with a role you give it on that one resource. Setup steps:
[Getting started](getting-started.md#3-connect-your-foundry-resource). Each
subscription connects one Foundry resource.

## What the router app can do

An administrator of your Microsoft Entra tenant approves *AI Cloud Gateway
Router* once. The app asks only for the basic sign-in permission Microsoft
requires (Microsoft Graph *User.Read*), which it never uses. It can reach only the
resource where you assign it the **Foundry User** role. With that role it can:

- call the models deployed on the resource;
- list the resource's deployments, read-only, so the dashboard can show them.

It cannot create, change or delete deployments, and cannot accept a model
provider's terms for you: deploying is always yours to do
([Models](models.md#deploy-a-model)). No keys or secrets are shared: the app
signs in to your tenant with its own certificate.

## Which resources work

- A Foundry (Azure AI Services) resource, or an Azure OpenAI resource. Other
  kinds are refused.
- It needs a **custom subdomain**, which Microsoft Entra sign-in requires. Set
  one on the resource's **Networking** page.
- It must accept **public network traffic**. A resource with public network
  access disabled cannot be reached, because the gateway calls it over the
  internet from AWS.
- Assign the role on the resource itself, not on a project inside it.

## Which deployments are routed

The router routes to a deployment when the model it runs is in our
[catalog](models.md#the-catalog). It matches on the model name Foundry reports
for the deployment (for example `gpt-5-mini`), so the deployment can have any
name. If two deployments run the same model, the first by name is used.

Deployments of models not in our catalog are listed on **Models** as *Not in our
catalog* and are never routed.

To keep a deployment out of routing, untick it under **Deployments the router
may use** on **Azure account** and select **Save routing**. A deployment you add
later is used once you select **Refresh** on **Models** (or **Check again** on
**Azure account**). You cannot exclude every deployment.

## Quotas

Each deployment's capacity is set in Azure. On Global Standard, a capacity of
1 means 1,000 tokens and 1 request a minute, so use 50 or more. If one model is
throttled, the request moves to the next; if all are, you get
`429 foundry_throttled`. Raise capacity in the Foundry portal or with the Azure
CLI.

## Disconnect

The dashboard has no disconnect button for a Foundry resource. To stop
routing:

- **At once:** revoke your API keys on **API keys**. They stop working within 60
  seconds.
- **On the Azure side:** remove the **Foundry User** role assignment and, if you
  like, delete the *AI Cloud Gateway Router* enterprise application from your
  tenant. Azure can keep honouring a removed role for over an hour.

To have the connection deleted from our side, email
**contact@aicloudgateway.com**.
