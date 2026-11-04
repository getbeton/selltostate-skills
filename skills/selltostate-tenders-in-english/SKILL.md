---
name: selltostate-tenders-in-english
description: Search in English, match local languages with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user wants tenders from countries that publish in other languages, searched and shown in English.
---

# Search in English, match local languages (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

Find open tenders in <countries> for <what my company sells>. Search in English and in the local languages of each country, and translate each title into English for me.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. For each country, write the search words in English and in the local language, joined with `OR` (for example `"road maintenance" OR "mantenimiento de carreteras"`).
2. Search each country: MCP `search` with `scope=tenders`, `q`, `country`, `accepts_bids=true`. REST: `GET https://app.selltostate.com/api/v1/search?scope=tenders&q=<words>&country=<slug>&accepts_bids=true`.
3. If `found` is 0, try `lifecycle=open`, then `published_days=30`.
4. Translate each title that is not in English. Mark the translation as yours and keep the original title.
5. Show title, buyer (the part of `sub` before " · "), country, `deadline_at`, value with its currency and `source_url`.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
