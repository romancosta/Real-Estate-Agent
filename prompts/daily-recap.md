# Evening check + daily recap (scheduled routine prompt)

> Placeholders: `{{DRIVE_FOLDER_ID}}`, `{{CRITERIA_DOC_ID}}`. Connectors required: Zillow, Google Drive.

Run every step of `prompts/check.md` first. Then:

1. **Today's activity** — `search_files` in `{{DRIVE_FOLDER_ID}}` for `title contains 'seen'` created since
   00:00 Eastern today and read them (reuse anything already read in step 2 of the check).
2. **Recap doc** — `create_file` in the folder, title `Daily recap YYYY-MM-DD`, `contentMimeType: text/markdown`:
   - **Act on these** — top 3 not-excluded listings seen in the last 4 days, each with score, key facts,
     link, why it fits, and the next step (book a tour, apply, ask about move-in date or laundry).
   - **New today** — table of every listing logged today.
   - **Market pulse** — fresh listings checked today vs. matches; any notable pattern (one or two lines).
   - **Timeline** — days until the move-in window opens, with a one-line nudge if it is getting close.
3. **Report** — the final message (push + email): count of new listings today, the top 3 in one line each
   with links, and a link to the recap doc.
