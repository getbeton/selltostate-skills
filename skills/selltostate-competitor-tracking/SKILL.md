---
name: selltostate-competitor-tracking
description: Competitor contract wins with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user asks which public contracts a competitor won, or which companies win contracts for a subject.
---

# Competitor contract wins (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

List the public contracts that <competitor name> won in the last 24 months: buyer, value, date and country. Then show the buyers that bought from them more than once.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Find the company: MCP `search` with `scope=suppliers`, `match=name`, `q=<company>`. Note its `slug` and `country`. A company can have one record per country.
2. Get its profile: MCP `get_supplier_detail` (`slug`, `country`), or `GET https://app.selltostate.com/api/v1/suppliers/{slug}?country=<slug>`.
3. Get its awards: MCP `search_awards` with `country` and `supplier_slug`. Use `year` for each year in the period.
4. Report the awards, the total value per currency, the top buyers and the buyers that bought more than once, with `source_url`.
5. Who wins a subject: MCP `search` with `scope=suppliers` and `q=<product>` (the default match is the subject of the won tenders).

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
