---
name: selltostate-contract-award-data
description: Contract awards: who won, at what price with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user asks who won government contracts for a product or service, at what price, or for award history.
---

# Contract awards: who won, at what price (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth, or the x-api-key of the agent sign-in: POST https://app.selltostate.com/api/v1/agent/signup, the user approves one code). Call `whoami`. `onboarding_required`: a new account; ask the user its questions and call `submit_onboarding` first. `active` false is a free account: `plan_search` and `search` only, 1-2 searches a day.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user picks a plan at https://app.selltostate.com/plans, or you run the agent signup in "Get access" of https://www.selltostate.com/skills/selltostate/SKILL.md. The user pays in the browser.

## Task

Show the government contract awards for <what my company sells> in <countries> in the last 12 months: who won, the buyer, the value and the date. Then list the top 10 suppliers by total value.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Search awards: MCP `search_awards` with `country` (one country per call), `q` and `awarded_days=365`. REST: `GET https://app.selltostate.com/api/v1/awards?country=<slug>&q=<words>&awarded_days=365&page_size=100`.
2. Use `amount_min` / `amount_max`, `supplier_slug`, `agency_slug` and `year` to narrow the list. Read `total` and page with `page`.
3. Show supplier, buyer, value with currency, award date and `source_url`.
4. Rank the top 10 suppliers by total value per currency. Do not add amounts in different currencies.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
