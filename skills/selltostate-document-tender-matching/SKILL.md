---
name: selltostate-document-tender-matching
description: Tender matches from your documents with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user gives a product sheet, catalogue or capability statement and asks for tenders that fit it.
---

# Tender matches from your documents (Sell to State)

This feature is in development. Do the steps below and tell the user that the full feature is not available yet.

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

When the Sell to State plan_search tool is available, read my product sheet and find tenders that fit it. Until then, search tenders for the products in this text: <paste>.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Read the document. Write the product names, the technical terms and the synonyms as short search phrases.
2. Search each phrase: MCP `search` with `scope=tenders`, `q`, `country`, `accepts_bids=true`. REST: `GET https://app.selltostate.com/api/v1/search?scope=tenders&q=<phrase>&accepts_bids=true`.
3. Merge the results, remove duplicates by `id`, and say which phrase matched each tender.
4. Show title, buyer (the part of `sub` before " · "), country, `deadline_at`, value with its currency and `source_url`.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
