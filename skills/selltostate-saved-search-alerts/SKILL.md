---
name: selltostate-saved-search-alerts
description: Saved searches and email alerts with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user wants to watch a tender search and get the new matches by email, webhook or Google Sheets.
---

# Saved searches and email alerts (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth, or the x-api-key of the agent sign-in: POST https://app.selltostate.com/api/v1/agent/signup, the user approves one code). Call `whoami`. `onboarding_required`: a new account; ask the user its questions and call `submit_onboarding` first. `active` false is a free account: `plan_search` and `search` only, 1-2 searches a day.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user picks a plan at https://app.selltostate.com/plans, or you run the agent signup in "Get access" of https://www.selltostate.com/skills/selltostate/SKILL.md. The user pays in the browser.

## Task

Test a Sell to State search for <what my company sells> in <countries>, save it, and give me its name and the link https://app.selltostate.com/alerts where I turn on the email alert.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Test the search first with MCP `search` (`scope=tenders`, `q`, `country`, `lifecycle=open`). Change `q` until the results are relevant.
2. Save it: MCP `save_search`, or `POST https://app.selltostate.com/api/v1/saved-searches` with JSON `{"name": "<name>", "kind": "atlas", "query": {"scope": "tenders", "q": "<words>", "country": "<slugs>", "lifecycle": "open"}}`.
3. A search saved through the API or MCP has the email alert off. Tell the user to turn it on, daily or weekly, at https://app.selltostate.com/alerts.
4. For a webhook or Google Sheets, send the user to https://app.selltostate.com/integrations.
5. List or change saved searches: MCP `get_saved_searches`, `rename_saved_search`, `delete_saved_search`.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
