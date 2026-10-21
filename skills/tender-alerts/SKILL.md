---
name: tender-alerts
description: Save a government tender search and get the new matches by email, webhook, Google Sheets or a polling job. Use when the user wants tender alerts, monitoring, a daily feed of new tenders, or to send new tenders to Slack, a CRM, a sheet, Zapier or n8n.
---

# Tender alerts

## Before you start

- Use the Sell to State MCP tools if they are connected (`search`, `save_search`). If not, call the REST API with `STS_API_KEY` (see the `sell-to-state-api` skill).
- Every data call needs an active Sell to State plan. On a 402, show the user the message and the link, then stop.

## Steps

1. Build the search and test it. Use the steps of the `find-open-tenders` skill. Show the user the first results and agree on the filters.
2. Save it:

   MCP `save_search`: `{"name": "UK payroll software", "kind": "atlas", "query": {"scope": "tenders", "q": "payroll software", "country": "united-kingdom", "lifecycle": "open"}}`

   REST:
   ```bash
   curl -s -X POST https://app.selltostate.com/api/v1/saved-searches \
     -H "x-api-key: $STS_API_KEY" -H "content-type: application/json" \
     -d '{"name":"UK payroll software","kind":"atlas","query":{"scope":"tenders","q":"payroll software","country":"united-kingdom","lifecycle":"open"}}'
   ```
3. A search saved through the API has alerts off. Tell the user the next step in the web app:
   - Email alerts, daily or weekly: https://app.selltostate.com/alerts
   - Webhook (a signed JSON POST of the matched rows) or Google Sheets: https://app.selltostate.com/integrations

## Polling job (your own script, Zapier, n8n)

Poll the search for the rows added since the last run:

```bash
curl -s "https://app.selltostate.com/api/v1/search?scope=tenders&q=payroll%20software&country=united-kingdom&sort=added_desc&added_after=$LAST_RUN_UNIX&per_page=50" \
  -H "x-api-key: $STS_API_KEY"
```

- Keep the time of each run in unix seconds. Send it as `added_after` in the next run.
- Remove duplicates by `slug`.
- If the API answers 400 `unknown_param` for `added_after`, use `published_days=7` and remove the rows that you saw before (by `slug`).
- Do not poll more than once per hour. The search limit is 300 calls per minute per key, shared by all your jobs.
