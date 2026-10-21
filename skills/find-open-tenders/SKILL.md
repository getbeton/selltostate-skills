---
name: find-open-tenders
description: Find government tenders that still accept bids, by keyword and country, with the bid deadline and the link to the official notice. Use when the user asks for open tenders, RFPs, RFQs, bids, public procurement opportunities or "what can I bid on" in one or more countries. Data from Sell to State (official procurement sources in more than 60 countries).
---

# Find open tenders

## Before you start

- If the Sell to State MCP tools are connected, use the `search` tool. If not, call the REST API with the key in `STS_API_KEY`. The `sell-to-state-api` skill has the details.
- Every data call needs an active Sell to State plan. If a call returns 402, show the user the message and the link in it, then stop.

## Steps

1. Get from the user: what they sell (2 to 5 keywords), the countries, and an optional sector. If the user gives no country, ask for one.
2. For each keyword, search tenders that accept bids, nearest deadline first:

   MCP `search`: `{"scope": "tenders", "q": "<keyword>", "country": "united-kingdom,ireland", "accepts_bids": true, "sort": "deadline", "per_page": 50}`

   REST:
   ```bash
   curl -s "https://app.selltostate.com/api/v1/search?scope=tenders&q=payroll%20software&country=united-kingdom,ireland&accepts_bids=true&sort=deadline&per_page=50" \
     -H "x-api-key: $STS_API_KEY"
   ```
3. If `found` is 0, search again with `lifecycle=open` instead of `accepts_bids` (some sources publish no deadline). If that is also 0, add `published_days=30` and drop the lifecycle filter to show recent notices.
4. Remove duplicate rows by `slug`. Remove rows whose title does not match what the user sells.
5. Show a table: title, buyer (the part of `sub` before " · "), country, `deadline_at`, `source_url`. Sort by deadline. Say how many rows you removed and why.

## Rules

- Country slugs are lowercase with hyphens: `united-kingdom`, `czech-republic`, `new-zealand`. Several countries go in one value, separated by commas. A wrong slug returns 400 `unknown_country`.
- To see which countries have data, run a search with no `q` and no `country` and read `facets.country`.
- One call takes one text. Run one call per keyword. `q` takes at most 120 characters.
- German, Dutch, Swedish and Finnish titles use compound words. Search the stem too, for example `datenbank` for "Datenbankmanagement".
- `accepts_bids` returns rows only where the source gives a deadline. For other countries, use `lifecycle=open` and tell the user to check the deadline on the official notice.
- Always give `source_url`. It is the official notice, where the user reads the documents and bids.
- Credit the data: "Data: Sell to State (selltostate.com)".
