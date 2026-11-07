---
name: selltostate-contract-renewal-forecast
description: Contract renewal forecast with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user asks which government contracts of a buyer or sector will come up for renewal.
---

# Contract renewal forecast (Sell to State)

This feature is in development. Do the steps below and tell the user that the full feature is not available yet.

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth, or the x-api-key of the agent sign-in: POST https://app.selltostate.com/api/v1/agent/signup, the user approves one code). Call `whoami`. `onboarding_required`: a new account; ask the user its questions and call `submit_onboarding` first. `active` false is a free account: `plan_search` and `search` only, 1-2 searches a day.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user picks a plan at https://app.selltostate.com/plans, or you run the agent signup in "Get access" of https://www.selltostate.com/skills/selltostate/SKILL.md. The user pays in the browser.

## Task

When the Sell to State renewal forecast is available, list the contracts of <buyer or sector> that are likely to come up for renewal in the next 12 months. Until then, show the awards of <buyer> in the last 3 years with supplier, value and date.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Find the buyer: MCP `search` with `scope=buyers` and `q=<buyer name>`. Note its `slug` and `country`.
2. Get its awards: MCP `search_awards` with `country`, `agency_slug` (or `q` for a sector) and `year` for each of the last 3 years. REST: `GET https://app.selltostate.com/api/v1/awards?country=<slug>&agency_slug=<slug>&year=<year>`.
3. List supplier, value with currency, award date and `source_url`, oldest first.
4. Say that this is the award history, not a forecast. Do not invent contract end dates.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
