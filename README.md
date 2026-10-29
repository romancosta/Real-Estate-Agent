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
 │ Evening check + daily recap  │   read / write logs   │ seen YYYY-MM-DD HHMM     │
 │ Weekly market recap          │ ◀───────────────────▶ │ Daily recap YYYY-MM-DD   │
 └──────────────┬───────────────┘                       │ Weekly recap YYYY-MM-DD  │
                │ zillow_property_search                └──────────────────────────┘
                ▼
        Zillow connector (MCP)             ──▶  push notification + email summary
```

Each run:
1. Reads the criteria doc (edit it from your phone; the next run picks it up).
2. Reads the last 4 days of `seen` logs so listings are only reported once.
3. Runs three narrow Zillow searches limited to listings ≤ 1 day old: base filters, in-unit laundry,
   and a `"shared laundry"` keyword search used to exclude listings that clearly lack in-unit W/D.
4. Scores new listings (layout, price incl. fees, size, laundry, baths) and logs them.
5. Ends with a short summary that Routines deliver as a push notification and email.

## Schedule (Eastern)

| Routine | When | Prompt |
|---|---|---|
| Morning check | daily 8:37 | [`prompts/check.md`](prompts/check.md) |
| Midday check | weekdays 1:17 pm | [`prompts/check.md`](prompts/check.md) |
| Evening check + daily recap | daily 6:43 pm | [`prompts/daily-recap.md`](prompts/daily-recap.md) |
| Weekly market recap | Sundays 12:23 pm | [`prompts/weekly-recap.md`](prompts/weekly-recap.md) |

About 20 runs a week. Off-the-hour minutes avoid scheduler congestion. Native Zillow/Redfin saved-search
alerts stay on as the real-time layer between runs.

## Why this design

- **Token-frugal**: filtering to listings ≤ 1 day old keeps each Zillow response to a few KB instead of
  ~95 KB for an unfiltered search (the connector returns up to 100 results and ignores `displayLimit`).
- **Precise geography**: a custom `MULTIPOLYGON` beats neighborhood names, which the connector resolved
  loosely (pulling in adjacent cities) during testing.
- **Stateless runs, durable state**: every run is a fresh session; dedupe state is append-only Google Docs
  because the Drive connector can create and read files but not edit them.
- **Private by construction**: the public repo only has placeholders (`{{DRIVE_FOLDER_ID}}`,
  `{{CRITERIA_DOC_ID}}`); the real values exist only inside the private routines.

See [`docs/connector-evaluation.md`](docs/connector-evaluation.md) for the data-source evaluation.

## Set up your own

1. Enable the **Zillow** and **Google Drive** connectors on claude.ai.
2. Create a private Drive folder, and inside it a Google Doc named `Criteria` from
   [`config/criteria.example.md`](config/criteria.example.md).
3. Create four Claude Code Routines (fresh session per run, Zillow + Google Drive connectors, push/email
   notifications on) using the prompts in [`prompts/`](prompts/), substituting your folder and doc IDs.
4. Turn on Zillow/Redfin saved-search instant alerts for the same area.
