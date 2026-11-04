---
name: selltostate-tender-automation
description: Automation with n8n and Activepieces with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user wants a scheduled workflow (n8n, Activepieces, Make, Zapier or a cron job) that sends new tenders to Slack, a CRM or a sheet.
---

# Automation with n8n and Activepieces (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

Build an n8n workflow that calls the Sell to State API every morning for new tenders that match <my saved search> and posts each one to <Slack channel or CRM>.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Trigger: a schedule, every morning.
2. Call: an HTTP request `GET https://app.selltostate.com/api/v1/search?scope=tenders&q=<words>&country=<slugs>&accepts_bids=true&published_days=1` with the header `x-api-key`.
3. Keep the `id` values that the workflow already sent, and send only new tenders.
4. Send each new tender to the target (Slack, CRM, sheet) with title, buyer (the part of `sub` before " · "), country, `deadline_at`, value with its currency and `source_url`.
5. No code: save the search and use the webhook or Google Sheets output at https://app.selltostate.com/integrations.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
