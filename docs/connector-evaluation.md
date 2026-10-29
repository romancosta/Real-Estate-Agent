# Data source evaluation

Evaluated October 2026 for an automated rental search (new-listing alerts + daily/weekly recaps).

## Zillow connector (claude.ai MCP) — chosen

| Strengths | Limitations |
|---|---|
| Rentals and for-sale; filters for beds, baths, sqft, rent, pets, in-unit laundry, price reductions, commute time, keywords | Max 100 results per call (~95 KB); `displayLimit` was ignored in testing |
| `daysOnZillow` filter makes "what's new since yesterday" cheap (~1–5 KB) | Results don't include days-on-market, listing date, description or availability date |
| `customPolygonWKT` (incl. `MULTIPOLYGON`) gives exact geography | Named-region search resolved loosely and pulled in adjacent cities |
| Neighborhood, Rent Zestimate and affordability tools for recaps | Only callable inside Claude sessions (not GitHub Actions / scripts) |
| Usable from scheduled Claude Code Routines via connector grants | Routines run at most hourly, so not real-time |

Workarounds used: dedupe by zpid (parsed from `homeDetailsPageUrl`), laundry inferred by combining an
`inUnitLaundry` search with a `"shared laundry"` keyword search, availability and lease terms flagged
for manual confirmation.

## Alternatives

| Option | Verdict |
|---|---|
| Zillow / Redfin saved-search instant alerts | Keep on — fastest signal, but no scoring, dedupe or recaps |
| RentCast API (paid) | Best option for a fully scripted pipeline (e.g. GitHub Actions); not needed while the connector works |
| Zillow Bridge / official APIs | Partner-only; not available to individuals |
| Scraping (Apify etc.) | Violates Zillow ToS and is brittle — rejected |

## State storage

Google Drive connector: can create and read Google Docs but cannot edit file content and cannot read
plain-text files back. Hence append-only `seen` / `actions` Google Docs, compacted daily into a `watchlist` snapshot.
