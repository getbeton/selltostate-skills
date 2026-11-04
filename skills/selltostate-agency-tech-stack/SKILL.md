---
name: selltostate-agency-tech-stack
description: Agency technology profile with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user asks which software and IT services a government agency buys, and from which suppliers.
---

# Agency technology profile (Sell to State)

This feature is in development. Do the steps below and tell the user that the full feature is not available yet.

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

When the Sell to State agency technology profile is available, tell me which software and IT services <agency> buys and from which suppliers. Until then, list its IT awards of the last 3 years.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Find the agency: MCP `search` with `scope=buyers` and `q=<agency>`. Note its `slug` and `country`.
2. Get its IT awards: MCP `search_awards` with `country`, `agency_slug` and `q=software OR licence OR license OR "IT services" OR cloud OR SaaS`, for each of the last 3 years (`year`).
3. Group the awards by supplier and by product words in the title. Show value with currency, date and `source_url`.
4. Say that the profile comes from award titles. Do not claim that the agency uses a product that no award names.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
