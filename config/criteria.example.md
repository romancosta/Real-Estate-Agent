# Home Search Criteria (EXAMPLE — placeholders only)

Copy this into a **private** Google Doc named `Criteria` inside your private Drive folder.
Every routine reads that doc at run time, so edits take effect on the next run without touching the routines.

## Hard filters (Zillow search)
- Type: rentals only (homeStatuses: forRent)
- Bedrooms: min <N> (<preferred layout>; fallbacks: <e.g. 2bd + den>)
- Bathrooms: min <N>
- Living area: min <N> sqft
- Rent: max $<N>/mo
- Lease: <N>+ months
- Move-in window: <start date> – <end date>

## Search area (customPolygonWKT — paste exactly)
MULTIPOLYGON(((<lon lat>, ...)), ((<lon lat>, ...)))

Covers: <neighborhood list>. Drop results clearly outside these areas.

## Laundry rule
- Re-run the search with the same filters: (a) rentalShowOnly ["inUnitLaundry"], (b) keywords "shared laundry".
- Tag listings in (a) as "W/D in unit".
- EXCLUDE listings in (b) but NOT in (a).
- Everything else: keep, tag "laundry unconfirmed".

## Scoring (0–100)
- Bedrooms: preferred = 40; fallback A = 30; fallback B = 25; other = 10
- Price: <= $<A> = 20; <= $<B> = 15; <= max = 8 (include required monthly fees)
- Size: >= <A> sqft = 15; >= <B> = 10; >= min = 5
- Laundry: in unit = 15; unconfirmed = 5
- Baths: >= 2 = 10; 1.5 = 5
- "Strong match" = score >= 70 -> push-worthy

## Notes / preferences
- <free text the agent should weigh>
