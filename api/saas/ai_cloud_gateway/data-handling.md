# Data handling

[AI Cloud Gateway](README.md) > Data handling

- **Where the service runs:** AI Cloud Gateway is hosted on AWS in `us-east-1`
  (US East, N. Virginia), not in Azure. Requests pass through it on their way to
  your Foundry resource, and answers on their way back.
- **Prompts and completions are never stored.** They pass through the gateway
  in memory and are never written to a log, database or file.
- **Inference runs on your Foundry resource**, in your Azure subscription, under
  your Azure terms and your deployments' content filters.
- **What is kept:** request and token counts per model per hour, and routing
  metadata (such as task type, difficulty score and the model chosen). It holds
  no prompt or response text, and powers the **Usage** page, billing and routing
  improvements.
- **Your connection:** your tenant ID, the resource ID and endpoint, and the
  list of its deployments (names, models, versions), with the ones you excluded.
- **Models:** the models you chose, the catalog's model ids when you saved (to
  mark later ones *New*), and each model's last [test](models.md#test-a-model)
  result: pass or fail, a short message and the time. The test prompt is a fixed
  one-line prompt, and its answer is not kept.
- **Budgets:** month-to-date counters per subscription and per API key:
  requests, tokens and estimated cost. They hold no prompt or response text,
  and are deleted about 40 days after the month ends.
- **Credentials:** API keys are stored only as a hash. You share no keys or
  secrets with us: the router app signs in to your tenant with its own
  certificate and gets a short-lived token for your resource.
  [What the app can do](azure-account.md#what-the-router-app-can-do).

If your data must not pass through the US, or must not leave Azure, note the
`us-east-1` pass-through.
