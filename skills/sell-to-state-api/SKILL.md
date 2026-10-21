---
name: sell-to-state-api
description: Reference for the Sell to State government procurement API and MCP server - authentication, endpoints, search filters, errors and limits. Use when you need tender, award, buyer or supplier data from Sell to State and no MCP tool is connected, or when you write code, a script or an automation against the Sell to State API.
---

# Sell to State API

Sell to State holds government tenders and contract awards from official procurement sources in more than 60 countries, in one schema. Every tender row links to the official notice.

## Access

- Base URL: `https://app.selltostate.com`
- REST auth: header `x-api-key: <key>`. The user creates a key at https://app.selltostate.com/account/keys. Read it from `STS_API_KEY`. Never print it.
- MCP: `https://app.selltostate.com/api/mcp` (streamable HTTP). OAuth with dynamic client registration; MCP clients find it through `/.well-known/oauth-protected-resource`.
- Data needs an active plan. A call without one returns 402 with `payment_link`.
- Docs and OpenAPI: https://docs.selltostate.com and `https://app.selltostate.com/api/openapi.json`

## Endpoints

| Need | REST | MCP tool |
|---|---|---|
| Search tenders, buyers or suppliers | `GET /api/v1/search` | `search` |
| Awards of one country (Postgres, exact filters) | `GET /api/v1/awards` (`.csv` for CSV) | `search_awards`, `export` |
| One tender | `GET /api/v1/tenders/{id}?country=` | `get_tender` |
| Buyer profile | `GET /api/v1/buyers/{slug}?country=` | `get_buyer` |
| Supplier profile | `GET /api/v1/suppliers/{slug}?country=` | `get_supplier_detail` |
| Sectors | `GET /api/v1/sectors`, `/sectors/{slug}` | `list_sectors`, `get_sector` |
| Account and plan | `GET /api/v1/whoami` | `whoami` |
| Saved searches | `/api/v1/saved-searches` | `save_search`, `get_saved_searches` |
| Lists | `/api/v1/lists` | `create_list`, `add_to_list`, `get_list` |

## `/api/v1/search` filters

| Param | Values |
|---|---|
| `scope` | `tenders` (default), `buyers`, `suppliers`, `sectors`, `countries` |
| `q` | text, at most 120 characters |
| `country` | slug, or slugs separated by commas (OR). Example: `united-kingdom,ireland` |
| `sector` | sector slugs, for example `it-software` |
| `lifecycle` | `open`, `awarded`, `planned`, `cancelled` (several = OR) |
| `accepts_bids` | `true`: open tenders with a deadline in the future |
| `published_days`, `awarded_days`, `date_days` | `1`, `7`, `30`, `90`, `365` |
| `year_min`, `year_max`, `amount_min`, `amount_max` | numbers |
| `sort` | `relevance` (default), `deadline`, `published_desc`, `added_desc` (tenders) |
| `added_after` | unix seconds: tenders added to Sell to State after this time |
| `match` | suppliers and buyers: `awards` (default, the subject of won tenders) or `name` |
| `page`, `per_page` | `per_page` from 1 to 50 |

Tender hit fields: `title`, `slug`, `sub` ("buyer · supplier"), `country`, `amount`, `year`, `lifecycle`, `deadline_at`, `accepts_bids`, `source_url`, `notice_kind`. The response also has `found` and `facets` (counts per country, sector, year, lifecycle).

## Errors and limits

| Status | Meaning | What to do |
|---|---|---|
| 400 `invalid_filter`, `unknown_param`, `unknown_country`, `unknown_sector` | A filter is wrong. `errors[]` lists each problem with `allowed` values | Fix the param and call again |
| 401 | No credential | Ask the user for a key or connect the MCP |
| 402 `payment_required` | No active plan | Show the message and `payment_link`. Stop |
| 429 | Too many calls | Wait for `Retry-After` seconds |

Limits: 300 search calls and 120 data calls per minute per key.

## Rules

- Amounts are in the source currency. Do not add different currencies.
- Credit the data: "Data: Sell to State (selltostate.com)". Link each tender to its `source_url`.
