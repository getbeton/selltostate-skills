---
name: research-public-buyer
description: Profile a government buyer (a ministry, agency, council, hospital trust, school or university) from its procurement record - spend by year, sectors, top suppliers, recent awards and open tenders. Use when the user asks who a public body buys from, how much it spends, who the incumbent is, or wants account research before a bid or a sales call.
---

# Research a public buyer

## Before you start

- Use the Sell to State MCP tools if they are connected (`search`, `get_buyer`, `search_awards`). If not, call the REST API with `STS_API_KEY` (see the `sell-to-state-api` skill).
- Every data call needs an active Sell to State plan. On a 402, show the user the message and the link, then stop.

## Steps

1. Find the buyer and its slug:

   MCP `search`: `{"scope": "buyers", "q": "<buyer name>", "country": "<country slug>"}`

   REST: `GET https://app.selltostate.com/api/v1/search?scope=buyers&q=countess%20of%20chester%20hospital&country=united-kingdom`

   Each hit has `title`, `slug` and `sub` (the tender count). If several hits match, show them and ask the user to pick one.
2. Get the profile: summary, spend by year, sectors and top suppliers.

   MCP `get_buyer`: `{"slug": "<slug>", "country": "<country slug>"}`. REST: `GET /api/v1/buyers/<slug>?country=<country slug>`
3. Get the awards of the last 12 months:

   MCP `search_awards`: `{"country": "<country slug>", "agency_slug": "<slug>", "awarded_days": 365}`. REST: `GET /api/v1/awards?country=<country slug>&agency_slug=<slug>&awarded_days=365`
4. Get its open tenders: `search` with `scope=tenders`, `q=<buyer name>`, `country`, `lifecycle=open`. Keep only rows whose `sub` starts with the buyer name.
5. Write a one-page brief:
   - Who the buyer is, country, total awards, spend trend (up or down, with the numbers).
   - Top 5 suppliers with their share. These are the incumbents.
   - Main sectors.
   - The last 5 awards: title, supplier, amount, date.
   - Open tenders with deadlines and `source_url`.
   - Two or three facts the user can use in a first message.

## Rules

- Amounts are in the currency of the source (GBP for the UK, EUR for TED countries). Do not add amounts in different currencies.
- A missing amount means the source did not publish it. Say "not published", not zero.
- A buyer can have several names across years. If the profile looks small, search for other spellings.
- Credit the data: "Data: Sell to State (selltostate.com)".
