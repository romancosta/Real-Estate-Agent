# Weekly market recap

Prepend [`_shared.md`](_shared.md).

1. Load state; also read `Daily recap` docs created since the previous `Weekly recap` doc (or last 7 days).
2. Search with window `daysOnZillow: {max: 7}`, plus one extra call D: base filters + `rentalShowOnly: ["priceReduction"]`, no day limit.
3. **Liveness**: run the base filters with no day limit once per POLYGON in the criteria's search area.
   If a call returned every match (`displayResultsCount == totalMatchingCount`), move FAVORITES/ACTIVE
   listings in that area that appear in neither its results (by zpid, or by building/address for `group`
   results) to GONE with note `not listed as of <date>`. If truncated, skip that area.
4. Log new listings (`seen` doc) and write a new `watchlist` snapshot.
5. `create_file` `Weekly recap YYYY-MM-DD` (`text/markdown`), numbered once across the doc:
   - **★ Favorites** — status, price changes, next step
   - **Top 5 on the watchlist** — with recommended action
   - **Gone since last recap** — listings moved to GONE
   - **Past 7 days by area** — new listings and price ranges per neighborhood; 3bd vs 2bd supply
   - **Price drops** — fits from D
   - **Criteria check** — too tight or loose given supply? Suggest tweaks (don't apply)
   - **Plan for the coming week** — countdown to the move-in window and what to do
6. Report: five-line summary using the doc's numbers, plus the doc link.
