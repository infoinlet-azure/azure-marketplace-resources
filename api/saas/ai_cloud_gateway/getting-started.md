# Getting started

[AI Cloud Gateway](README.md) > Getting started

Five steps. You need:

- A Microsoft Foundry resource with a custom subdomain and at least one chat
  model deployed, in an Azure subscription of your tenant. Your models run on
  that resource.
- An administrator of your Microsoft Entra tenant (Cloud Application
  Administrator or higher) for one approval.
- Owner or User Access Administrator on the Foundry resource, to assign a role.

## 1. Subscribe and activate

1. In Microsoft Marketplace, subscribe to **AI Cloud Gateway - LLM Router for
   Microsoft Foundry** and pick a plan and term.
2. Select **Configure account now**. Later, the same button is on the SaaS
   subscription in the Azure portal as **Configure account**. You arrive at
   `dashboard.aicloudgateway.com/marketplace/azure/`.
3. Select **Create account** or **I have an account**. Both offer **Sign in with
   Microsoft** (work or personal account), or an email and password.
4. Select **Complete subscription**. Billing starts here, not at purchase. A
   subscription can be linked to only one account.

## 2. Create an API key

On **API keys**, create a key. It starts with `acg_` and is **shown once**, so
store it now.

## 3. Connect your Foundry resource

On **Azure account**, follow the three steps:

1. **Allow AI Cloud Gateway Router in your tenant.** Select **Open Microsoft's
   approval page**. A tenant administrator approves it once; if that is not
   you, copy the link and send it to them. The app asks only for the basic
   sign-in permission Microsoft requires (Microsoft Graph *User.Read*), which
   it never uses.
2. **Give it access to your Foundry resource.** Assign the app the
   **Foundry User** role on the resource. The page shows the Azure CLI
   commands, or use **Access control (IAM)** > **Add role assignment** in the
   Azure portal and search for *AI Cloud Gateway Router*.
3. **Check the connection.** Paste the resource ID (the commands print it;
   the tenant ID is filled in from step 1) and select **Check and save**.

Azure can take several minutes to apply a new role. Meanwhile the page shows
"Waiting for Azure to apply the role" and checks again on its own. When it shows
**Connected**, it lists the resource's deployments.
[What the app can do](azure-account.md#what-the-router-app-can-do).

## 4. Check your models

Open **Models**. The router routes only to models **deployed** on your resource.
Models we suggest deploying are marked with a star. To add one, select
**Deploy** on it, run the command shown (or deploy from the Foundry portal), then
select **Refresh**. Select **Test** to send one tiny prompt to a model.

Optional for now: requests go to the models you already have deployed.
[Models](models.md) explains deploying, choosing and testing.

## 5. Make your first call

```bash
export ACG_API_KEY="acg_..."

curl https://api.aicloudgateway.com/v1/chat/completions \
  -H "Authorization: Bearer $ACG_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model": "auto", "messages": [{"role": "user", "content": "What is a sports car?"}]}'
```

You get an answer, and `model` names the catalog model that gave it, for
example `openai.gpt-5-nano`.

If you get an error instead:

| `code` | Go back to |
|---|---|
| `api_key_required`, `invalid_api_key` | Step 2 |
| `subscription_inactive` | Step 1: complete the subscription, or check it in the Azure portal |
| `foundry_not_connected` | Step 3 |
| `foundry_access_denied`, `foundry_consent_missing` | Step 3: the approval or the role |
| `foundry_no_routable_deployments` | Step 4: deploy a catalog model |

Anything else: [Troubleshooting](troubleshooting.md).

## Next

- [Examples](examples.md) - your language or framework, streaming, tools, JSON.
- [How routing works](routing.md) - choosing between cost and quality.
- [Models](models.md) - deploy, choose and test the models your routing uses.
- [Budgets and content filters](controls.md) - optional: cap monthly spend or
  requests.
