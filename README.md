# Real Estate Agent

An always-on rental search assistant built with **Claude Code Routines** and the **Zillow connector**.
It checks for new listings a few times a day, scores them against my criteria, sends push + email alerts
for strong matches, and writes daily and weekly market recaps — so a good place doesn't slip by on the
days I'm not searching manually.

> My actual criteria (budget, neighborhoods, search polygon) and all results are **not** in this repo.
> They live in a private Google Drive folder that the routines read at run time. This repo holds the
> design, the prompts, and an example criteria template.

## How it works

```
 Claude Code Routines (cloud, cron)                       Private Google Drive folder
 ┌──────────────────────────────┐   read criteria       ┌──────────────────────────┐
 │ Morning / midday check       │ ────────────────────▶ │ Criteria (Google Doc)    │
 │ Evening check + daily recap  │   read / write logs   │ watchlist / seen / actions│
 │ Weekly market recap          │ ◀───────────────────▶ │ Daily recap YYYY-MM-DD   │
 └──────────────┬───────────────┘                       │ Weekly recap YYYY-MM-DD  │
                │ zillow_property_search                └──────────────────────────┘
                ▼
        Zillow connector (MCP)             ──▶  push notification + email summary
```

Each run:
1. Loads state: the newest `watchlist` snapshot plus any `seen` / `actions` docs written since, the
   criteria doc, and the user-edited **Dismiss & favorites** doc.
2. Runs three narrow Zillow searches limited to fresh listings: base filters, in-unit laundry, and a
   `"shared laundry"` keyword search used to exclude listings that clearly lack in-unit W/D.
3. Scores listings it hasn't seen before (layout, price incl. fees, size, laundry, baths) and logs them.
4. Ends with a short numbered summary that Routines deliver as a push notification and email.

## Watchlist, dismissals and favorites

Every listing that passes the filters joins the **watchlist** and is re-recommended in daily recaps until
it is dismissed or leaves the market. Each listing carries a number in the run's message and a stable
5-digit code (last digits of its Zillow ID).

| Action | Reply in the run's session | Or add a line to the Drive doc |
|---|---|---|
| Dismiss forever | `x 1 3 4` | `x 80313 too far` |
| Favorite (pinned, tracked for price drops / off-market) | `fav 2` | `fav 80313` |
| Un-favorite | `unfav 2` | `unfav 80313` |
| Restore a dismissed listing | `undo 4` | `undo 80313` |

Chat replies are written to an `actions …` doc; the evening and weekly runs fold everything into a new
`watchlist …` snapshot (sections FAVORITES, ACTIVE, DISMISSED, EXCLUDED, GONE), so each run only reads the
latest snapshot plus a few small deltas. The weekly run also moves listings no longer on Zillow to GONE.

## Schedule (Eastern)

| Routine | Example timing | Prompt |
|---|---|---|
| Morning check | daily 8:37 | [`prompts/check.md`](prompts/check.md) |
| Midday check | weekdays 1:17 pm | [`prompts/check.md`](prompts/check.md) |
| Evening check + daily recap | daily 6:43 pm | [`prompts/daily-recap.md`](prompts/daily-recap.md) |
| Weekly market recap | Sundays 12:23 pm | [`prompts/weekly-recap.md`](prompts/weekly-recap.md) |

Timings are only set in the Routines UI — no prompt depends on them, so they can be changed freely
(search windows are derived from when state was last written). About 20 runs a week as configured. Off-the-hour minutes avoid scheduler congestion. Native Zillow/Redfin saved-search
alerts stay on as the real-time layer between runs.

## Why this design

- **Token-frugal**: filtering to listings ≤ 1 day old keeps each Zillow response to a few KB instead of
  ~95 KB for an unfiltered search (the connector returns up to 100 results and ignores `displayLimit`).
- **Precise geography**: a custom `MULTIPOLYGON` beats neighborhood names, which the connector resolved
  loosely (pulling in adjacent cities) during testing.
- **Stateless runs, durable state**: every run is a fresh session. The Drive connector can create and read
  Google Docs but not edit them, so state is event-sourced: append-only `seen` / `actions` docs, periodically
  compacted into a `watchlist` snapshot.
- **Private by construction**: the public repo only has placeholders (`{{DRIVE_FOLDER_ID}}`,
  `{{CRITERIA_DOC_ID}}`, `{{ACTIONS_DOC_ID}}`); the real values exist only inside the private routines.

See [`docs/connector-evaluation.md`](docs/connector-evaluation.md) for the data-source evaluation.

## Set up your own

1. Enable the **Zillow** and **Google Drive** connectors on claude.ai.
2. Create a private Drive folder containing a `Criteria` Google Doc (from
   [`config/criteria.example.md`](config/criteria.example.md)), an empty `Dismiss & favorites (edit me)` doc,
   and an initial `watchlist` snapshot doc.
3. Create four Claude Code Routines (fresh session per run, Zillow + Google Drive connectors, push/email
   notifications on) using [`prompts/_shared.md`](prompts/_shared.md) + the routine-specific prompt, substituting your folder and doc IDs.
4. Turn on Zillow/Redfin saved-search instant alerts for the same area.
