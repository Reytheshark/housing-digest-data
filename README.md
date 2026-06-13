# housing-digest-data

State store for the Housing Market Intelligence Digest routine (daily + weekly).

**Scope:** townhouses & low-HOA condos (<=$400/mo dues), $400K-$750K, in San Diego (city, Carmel Valley 92130, UTC 92122), Loudoun County VA (Arcola 20105), Fairfax County VA (Oakton HS attendance area), Dallas TX, and Austin TX.

## Layout

```
data/
  metrics.csv      # daily market metrics, one row per area per day (append-only)
  rentals.csv      # daily rental metrics, one row per micro-market per day (append-only)
  listings.json    # watchlist: tracked listings with price/status history
digests/
  daily/           # archived daily digests, YYYY-MM-DD.md
  weekly/          # archived weekly digests, YYYY-Wnn.md
archive/
  listings/        # closed/delisted watchlist entries older than 90 days
```

## Conventions

- `area` values: san_diego_city | carmel_valley | utc | loudoun_co | arcola | fairfax_co | oakton_hs | dallas | austin
- Time series are append-only; corrections are noted in commit messages.
- Blank fields mean data was unavailable that day; values are never interpolated.
- GitHub is the system of record; the Google Sheet below is a convenience mirror.

## Linked resources

- Google Sheet mirror: "Housing Digest Dashboard", spreadsheet ID `189906qIJo_KxlUQIrabWORetIMZ7kn9U4MgVXuoMM_Q` (tabs: Metrics, Rentals, Watchlist, Weekly)
- Raw-read base URL: `https://raw.githubusercontent.com/Reytheshark/housing-digest-data/main/`

Maintained automatically; manual edits are fine but keep the schemas intact.
