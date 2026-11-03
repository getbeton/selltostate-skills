---
name: selltostate
description: Use Sell to State for government tenders and contract awards from official procurement sources in more than 60 countries. Covers signup and payment through the agent, then the four main jobs - search tenders, save a search with email alerts, export rows to CSV, and see who wins (competitor and buyer research). Use when the user asks for public tenders, RFPs, bids, contract awards, who won a contract, a buyer brief, a competitor's government wins, or wants to sign up for Sell to State.
---

# Sell to State

Sell to State holds government tenders and contract awards from official procurement sources in more than 60 countries, in one schema. Every tender links to the official notice (`source_url`).

- App: https://app.selltostate.com
- REST API: `https://app.selltostate.com/api/v1`, header `x-api-key: <key>`
- MCP server: `https://app.selltostate.com/api/mcp` (OAuth)
- Prices: `GET https://app.selltostate.com/api/v1/prices` (no key)
- OpenAPI: `https://app.selltostate.com/api/openapi.json`

## 0. Get access

Use the first path that works:

1. The MCP tools are connected: call `whoami`. If `active` is true, go to the jobs below.
2. The user has an API key: read it from `STS_API_KEY`. Never print it. Call `GET /api/v1/whoami`.
3. No access yet: sign the user up through the agent signup (RFC 8628 device flow):
   1. `GET /api/v1/prices`. Show the plans: the 3-day pass (one payment), the subscription (monthly or annual), Enterprise (book a call). Prices are in USD and EUR.
   2. When the user picks a plan:
      ```bash
      curl -s -X POST https://app.selltostate.com/api/v1/agent/signup \
        -H "content-type: application/json" \
        -d '{"client_name": "<your agent name>", "plan": "monthly", "currency": "usd"}'
      ```
      `plan` is `pass`, `monthly` or `annual`. `currency` is `usd` or `eur`. Both are optional.
   3. Give the user `verification_uri_complete` and `user_code`. The user opens the link, signs in with Google or email, checks that the code matches, approves and pays in Stripe Checkout.
   4. Every `interval` seconds (5), poll:
      ```bash
      curl -s -X POST https://app.selltostate.com/api/v1/agent/token \
        -H "content-type: application/json" -d '{"device_code": "<device_code>"}'
      ```
      | `error` | Do this |
      |---|---|
      | `authorization_pending` | The user has not approved yet. Poll again |
      | `payment_pending` | Approved, not paid yet. Poll again |
      | `slow_down` | Wait 10 seconds, then poll |
      | `expired_token` | Start again at step 2 |
      | `access_denied` | The user said no. Stop |
      | `invalid_grant` | The code is unknown or already used. Start again |
   5. On success you get `access_token`. It is an API key: send it as `x-api-key`. Keep it secret. The key is shown once.
4. Enterprise: there is no self-serve signup. Give the user https://cal.com/beton/discovery-call.

A data call with no active plan returns 402. Show the message and the link, then stop.

## 1. Search tenders

MCP `search`, or REST `GET /api/v1/search`:

```bash
curl -s "https://app.selltostate.com/api/v1/search?scope=tenders&q=%22body%20camera%22%20OR%20bodycam&country=kenya,south-africa&accepts_bids=true&sort=deadline&per_page=50" \
  -H "x-api-key: $STS_API_KEY"
```

- `q` takes plain words or boolean syntax: `OR`, `AND`, `NOT`, `"exact phrase"`, parentheses, `-word`. At most 2,000 characters.
- Search in the local languages too: add the local words with `OR` (for example `"road maintenance" OR "mantenimiento de carreteras"`).
- `country`: lowercase slugs with hyphens, several separated by commas. To see the countries with data, search with no `q` and no `country` and read `facets.country`.
- Open tenders: `accepts_bids=true` (only sources with a deadline). If `found` is 0, use `lifecycle=open`, then `published_days=30`.
- Show: title, buyer (the part of `sub` before " · "), country, `deadline_at`, value with its currency, `source_url`.

## 2. Save a search and turn on alerts

MCP `save_search`, or REST:

```bash
curl -s -X POST https://app.selltostate.com/api/v1/saved-searches \
  -H "x-api-key: $STS_API_KEY" -H "content-type: application/json" \
  -d '{"name": "Kenya bodycams", "kind": "atlas", "query": {"scope": "tenders", "q": "\"body camera\" OR bodycam", "country": "kenya", "lifecycle": "open"}}'
```

A search saved through the API or MCP has the email alert off. Tell the user to turn it on, daily or weekly, at https://app.selltostate.com/alerts. For a webhook or Google Sheets: https://app.selltostate.com/integrations.

## 3. Export to CSV

MCP `export`, or REST `GET /api/v1/awards.csv?country=<slug>&q=<words>&...` with the same filters as `/api/v1/awards`. The subscription gives up to 5,000 rows per export; the 3-day pass has 10,000 rows in total. Give the user the file and say how many rows it has.

## 4. Who wins: competitors and buyers

- A company's wins: find it with `search` (`scope=suppliers`, `match=name`), then `get_supplier_detail` (`slug`, `country`) and `search_awards` (`country`, `supplier_slug`). Report awards, total value per currency, top buyers, sectors, newest and largest awards with `source_url`.
- Who wins a subject: `search` with `scope=suppliers` and `q=<product>` (the default match is the subject of the won tenders).
- A buyer brief: `search` with `scope=buyers`, then `get_buyer` / `GET /api/v1/buyers/{slug}?country=`: spend, top suppliers, sectors, contracts by year, open tenders.

## Rules

- Amounts are in the source currency. Do not add amounts in different currencies.
- The data shows awards, not bids. Do not say that a company lost a tender.
- Always give `source_url`: the user reads the documents and bids on the official portal.
- Limits: 300 search calls and 120 data calls per minute per key. On 429, wait `Retry-After` seconds.
- Credit the data: "Data: Sell to State (selltostate.com)".
- More skills (open tenders, buyer research, competitor wins, alerts, API reference): `npx skills add getbeton/selltostate-skills`.
