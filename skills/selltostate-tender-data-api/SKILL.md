---
name: selltostate-tender-data-api
description: REST API with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user wants to call the Sell to State REST API from code, a script or a pipeline.
---

# REST API (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

Using the Sell to State REST API with my API key, fetch the awards of the last 7 days for <what my company sells> in <countries> and push them to <my CRM or sheet>.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Base URL https://app.selltostate.com/api/v1. Send `x-api-key: $STS_API_KEY` on every call. Check the plan: `GET https://app.selltostate.com/api/v1/whoami`.
2. Tenders: `GET https://app.selltostate.com/api/v1/search?scope=tenders&q=<words>&country=<slugs>&accepts_bids=true&published_days=7`. Awards: `GET https://app.selltostate.com/api/v1/awards?country=<slug>&q=<words>&awarded_days=7&page=1&page_size=100`.
3. Page through the results until you have `total` rows. Remove duplicates by `id`.
4. The full spec: https://app.selltostate.com/api/openapi.json.
5. Limits: 300 search calls and 120 data calls per minute per key. On 429, wait `Retry-After` seconds.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
