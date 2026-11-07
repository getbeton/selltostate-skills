---
name: selltostate-search-government-contracts
description: Search tenders in 70+ countries with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user asks for open government tenders, RFPs or bids for a product or service in one or more countries.
---

# Search tenders in 70+ countries (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth, or the x-api-key of the agent sign-in: POST https://app.selltostate.com/api/v1/agent/signup, the user approves one code). Call `whoami`. `onboarding_required`: a new account; ask the user its questions and call `submit_onboarding` first. `active` false is a free account: `plan_search` and `search` only, 1-2 searches a day.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user picks a plan at https://app.selltostate.com/plans, or you run the agent signup in "Get access" of https://www.selltostate.com/skills/selltostate/SKILL.md. The user pays in the browser.

## Task

Search open government tenders for <what my company sells> in <countries>, open for bids, and list title, buyer, deadline, value and the official notice link.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Search: MCP `search` with `scope=tenders`, `q`, `country` (lowercase slugs, comma-separated), `accepts_bids=true`, `sort=deadline`. REST: `GET https://app.selltostate.com/api/v1/search?scope=tenders&q=<words>&country=<slugs>&accepts_bids=true&sort=deadline&per_page=50`.
2. `q` takes plain words or boolean syntax: `OR`, `AND`, `NOT`, `"exact phrase"`, parentheses. Add synonyms with `OR`.
3. If `found` is 0, try `lifecycle=open`, then `published_days=30`.
4. Show title, buyer (the part of `sub` before " · "), country, `deadline_at`, value with its currency and `source_url`.
5. For the full record of one tender: MCP `get_tender`, or `GET https://app.selltostate.com/api/v1/tenders/{id}`.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
