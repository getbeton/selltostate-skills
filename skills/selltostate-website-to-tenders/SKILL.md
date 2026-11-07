---
name: selltostate-website-to-tenders
description: Tender matches from your website with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user gives a company website and asks which government tenders fit that company.
---

# Tender matches from your website (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth, or the x-api-key of the agent sign-in: POST https://app.selltostate.com/api/v1/agent/signup, the user approves one code). Call `whoami`. `onboarding_required`: a new account; ask the user its questions and call `submit_onboarding` first. `active` false is a free account: `plan_search` and `search` only, 1-2 searches a day.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user picks a plan at https://app.selltostate.com/plans, or you run the agent signup in "Get access" of https://www.selltostate.com/skills/selltostate/SKILL.md. The user pays in the browser.

## Task

Find government tenders that match my company website <URL> in <countries>, and show the open ones first.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Read the website. Write 5 to 10 short search phrases for the products and services that the company sells, with synonyms.
2. Search each phrase: MCP `search` with `scope=tenders`, `q`, `country`, `accepts_bids=true`. REST: `GET https://app.selltostate.com/api/v1/search?scope=tenders&q=<phrase>&country=<slugs>&accepts_bids=true`.
3. Merge the results and remove duplicates by `id`. Put open tenders first, nearest deadline first.
4. For each tender, say which phrase matched it. Show title, buyer (the part of `sub` before " · "), country, `deadline_at`, value with its currency and `source_url`.
5. Ask the user to remove the phrases that give wrong matches, then search again.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
