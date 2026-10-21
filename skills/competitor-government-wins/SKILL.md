---
name: competitor-government-wins
description: Find the government contracts that a company won - a competitor, a partner or a prospect - by country, buyer, sector and value. Also find which companies win public contracts for a product or service. Use when the user asks "which government contracts did X win", "who sells X to the government", wants competitor or incumbent intelligence, or a list of public-sector accounts where a competitor sells.
---

# Competitor government wins

## Before you start

- Use the Sell to State MCP tools if they are connected (`search`, `get_supplier_detail`, `search_awards`). If not, call the REST API with `STS_API_KEY` (see the `sell-to-state-api` skill).
- Every data call needs an active Sell to State plan. On a 402, show the user the message and the link, then stop.

## A. Contracts that one company won

1. Find the company as a supplier. Search by name, in each country of interest or with no country:

   MCP `search`: `{"scope": "suppliers", "q": "<company name>", "match": "name"}`

   REST: `GET https://app.selltostate.com/api/v1/search?scope=suppliers&q=rohde%20schwarz&match=name`

   A group often wins under several legal entities (GmbH, Ltd, SAS) and in several countries. Keep every hit that is the same group. Ask the user when you are not sure.
2. For each hit, get the profile: summary, awards by year, sectors and top buyers.

   MCP `get_supplier_detail`: `{"slug": "<slug>", "country": "<country of the hit>"}`. REST: `GET /api/v1/suppliers/<slug>?country=<country>`
3. Get the award list:

   MCP `search_awards`: `{"country": "<country>", "supplier_slug": "<slug>"}`. REST: `GET /api/v1/awards?country=<country>&supplier_slug=<slug>&page_size=100`
4. Report per country: number of awards, total value (in the source currency), top buyers, main sectors, the newest and the largest awards with `source_url`.

## B. Companies that win contracts for a subject

Search suppliers by the subject of the tenders they won (the default match):

MCP `search`: `{"scope": "suppliers", "q": "<product or service>", "country": "<country slug>"}`

REST: `GET /api/v1/search?scope=suppliers&q=cybersecurity&country=germany`

Each hit is a company that won tenders whose subject matches. `sub` shows how many it won. Use part A on the top hits.

## Rules

- Supplier names come from the official notices. Spellings differ. Search short forms too ("Rohde" for "Rohde & Schwarz").
- Do not add amounts in different currencies.
- Do not claim that a company "lost" a tender. The data shows awards, not bids.
- Credit the data: "Data: Sell to State (selltostate.com)".
