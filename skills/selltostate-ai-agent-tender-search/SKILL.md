---
name: selltostate-ai-agent-tender-search
description: AI agents: Claude, ChatGPT and MCP with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user wants their AI agent (Claude, ChatGPT, Cursor or another MCP client) to search tenders and awards.
---

# AI agents: Claude, ChatGPT and MCP (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

Find open tenders for <what my company sells> in <countries>, show who won similar contracts in the last 12 months, and save the search so I can turn on its email alert.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Connect the MCP server https://app.selltostate.com/api/mcp (OAuth). The user signs in and picks a plan in the browser.
2. Call `whoami`. If `active` is false, show the message and the link, then stop.
3. Open tenders: `search` with `scope=tenders`, `q`, `country`, `accepts_bids=true`. Awards: `search_awards` with `country` and `q`.
4. Who won: `search` with `scope=suppliers`, then `get_supplier_detail`. Buyers: `search` with `scope=buyers`, then `get_buyer`.
5. Save the search with `save_search`. The user turns on its email alert at https://app.selltostate.com/alerts.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
