# Listing check

Prepend [`_shared.md`](_shared.md). Placeholders are filled only in the private routine.

1. Load state.
2. Search with window `daysOnZillow: {max: N}`, where N = whole days since the newest `seen` or `watchlist` doc,
   rounded up (min 1, max 7). This keeps the window correct however the schedule is set.
3. New = not known. If any, log them in a `seen YYYY-MM-DD HHMM` doc (ACTIVE / EXCLUDED lines).
4. Report only new, non-excluded listings, best first. Nothing new → `No new matches (<n> fresh listings checked).`
   Dismissed listings never appear; favorites are not repeated here.
