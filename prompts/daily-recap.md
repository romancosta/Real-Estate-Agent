# Check + daily recap

Prepend [`_shared.md`](_shared.md).

1. Run every step of [`check.md`](check.md) except the report.
2. Write a new snapshot `watchlist YYYY-MM-DD HHMM` with the full applied state (including this run's new listings).
3. `create_file` `Daily recap YYYY-MM-DD` (`text/markdown`), numbering listings once across the doc:
   - **★ Favorites** — each with next step (tour, apply, confirm move-in date / 12-month lease / laundry)
   - **New since last recap** — listings whose `first seen` is after the previous `Daily recap` doc's creation (or last 24 h)
   - **Watchlist** — every ACTIVE listing, best first (re-recommended until dismissed or gone)
   - **Market pulse** — fresh listings checked vs. matches since the last recap (1–2 lines)
   - **Timeline** — days until the move-in window opens; nudge if close
4. Report: new since last recap, favorites, then the top 5 of the watchlist, using the recap doc's numbers, plus the doc link.
