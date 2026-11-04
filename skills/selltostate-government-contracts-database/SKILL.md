---
name: selltostate-government-contracts-database
description: Buyers, suppliers and awards database with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user asks for a brief on a public buyer, or for data on government buyers, suppliers and awards.
---

# Buyers, suppliers and awards database (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

Using the Sell to State database, give me a brief on <buyer name>: total spend, top suppliers, top sectors, contracts by year and its open tenders.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Find the buyer: MCP `search` with `scope=buyers` and `q=<buyer name>`. Note its `slug` and `country`.
2. Get the brief: MCP `get_buyer` / `get_buyer_detail`, or `GET https://app.selltostate.com/api/v1/buyers/{slug}?country=<slug>`: spend, top suppliers, sectors, contracts by year.
3. Get its open tenders: MCP `search` with `scope=tenders`, `q=<buyer name>`, `country`, `accepts_bids=true`.
4. For a sector view: MCP `list_sectors` and `get_sector`. For lists: `list_buyers`, `list_suppliers`.
5. Give totals per currency and `source_url` for each award or tender.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
