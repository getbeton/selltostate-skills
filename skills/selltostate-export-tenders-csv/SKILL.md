---
name: selltostate-export-tenders-csv
description: CSV export with Sell to State data (government tenders and contract awards from official procurement sources). Use when the user wants government tenders or awards as a CSV file.
---

# CSV export (Sell to State)

## Access

1. MCP: the server https://app.selltostate.com/api/mcp (OAuth). Call `whoami`; `active` must be true.
2. REST: https://app.selltostate.com/api/v1 with the header `x-api-key`. Read the key from `STS_API_KEY` and never print it.
3. No plan yet: the user creates an account and picks a plan at https://app.selltostate.com/plans. The user pays in the browser.

## Task

Export the Sell to State awards for <what my company sells> in <countries> since <date> to CSV with buyer, supplier, value and date, and give me the file.

Ask the user for each value in <angle brackets> before the first call.

## Steps

1. Test the filters first with MCP `search_awards` and read `total`.
2. Export: MCP `export`, or `GET https://app.selltostate.com/api/v1/awards.csv?country=<slug>&q=<words>&awarded_days=365` with the same filters as `/awards`.
3. The subscription gives up to 5,000 rows per export. The 3-day pass has 10,000 rows in total. If `total` is larger, split the export by `year` or by `amount_min` / `amount_max`.
4. Give the user the file and say how many rows it has.

## Rules

- Give `source_url` (the official notice) for each tender and award. The user reads the documents and bids on the official portal.
- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- On a 402, show the message and the link to the user, then stop. On a 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".

More skills (open tenders, buyer research, competitor wins, alerts, API reference): https://github.com/getbeton/selltostate-skills
