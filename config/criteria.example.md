# Home Search Criteria (EXAMPLE — placeholders only)

Put this in a **private** Google Doc titled exactly `Criteria` inside your private Drive folder.
Routines always read the newest doc with that title, so a new version can be issued as a new doc.

## Base filters (use in every search call)
- homeStatuses: ["forRent"]
- bedrooms: min <N>
- bathrooms: min <N>
- rentalMonthlyPayment: max <N>
- rentalAvailabilityDate: <last acceptable move-in date>
- Do NOT set livingAreaSqft in the search — size is applied in the filter rules below.
- Search area (customPolygonWKT, paste exactly):
MULTIPOLYGON(((<lon lat>, ...)), ((<lon lat>, ...)))

## Areas
- <Area 1>: <neighborhoods, hard boundaries such as "nothing north of <road> (lat X)">
- <Area 2>: <...>
- Special zones (e.g. BEACH) as lat/long rectangles, used by the filter rules and scoring.

## Search plan (each call = base filters + the extra shown)
- A: nothing extra
- B: rentalShowOnly ["inUnitLaundry"]
- C: keywords "shared laundry"
- E: conditional keyword search, e.g. keywords "den", only when a rule needs the evidence

## Filter rules
- Size: min <N> sqft, with any zone-specific exceptions
- Laundry: in C but not in B → EXCLUDE; in B → "W/D in unit"; else "laundry unconfirmed"
- Lease: <N>+ months
- Move-in window: <start> – <end>

## Scoring (0–100, cap at 100)
- Bedrooms / layout, price incl. required fees, size, laundry, baths, zone bonus
- "Strong match" = score >= 70

## Notes / preferences
- <free text the agent should weigh>
