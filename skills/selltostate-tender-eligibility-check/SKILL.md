---
name: selltostate-tender-eligibility-check
description: Eligibility check for each tender with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user asks if their company can bid on a tender.
---

# Eligibility check for each tender (Sell to State)

This feature is in development. Do the steps below and tell the user that the full feature is not available yet.

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth, or the x-api-key of the agent sign-in: POST https://app.selltostate.com/api/v1/agent/signup, the user approves one code). Call `whoami`. `onboarding_required`: a new account; ask the user its questions and call `submit_onboarding` first. `active` false is a free account: `plan_search` and `search` only, 1-2 searches a day.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user picks a plan at https://app.selltostate.com/plans, or you run the agent signup in "Get access" of https://www.selltostate.com/skills/selltostate/SKILL.md. The user pays in the browser.

## Task

When the Sell to State eligibility check is available, tell me if a <country of my company> company can bid on tender <tender link>, and why. Until then, read the tender and list its participation conditions.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Get the tender: MCP `get_tender`, or `GET https://app.selltostate.com/api/v1/tenders/{id}`. Read `source_url`.
2. Open the official notice and its documents at `source_url`. List the participation conditions: registration, location, turnover, certificates, past contracts, deadlines.
3. Compare each condition with what the user told you about the company. Mark each one: met, not met, unknown.
4. Do not give a final yes. The buyer decides. Tell the user to check the documents on the official portal.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
