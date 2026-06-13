# Housing Market Intelligence Digest — Routine Instructions

A daily research routine plus a weekly rollup covering townhouses and low-HOA condos ($400K–$750K) across four metro markets, with micro-market comparisons against their parent city/county markets.

---

## 1. Scope & Definitions

**Property types:** Townhouses (attached, fee-simple or condo-regime) and condos with **low HOA**, defined as **≤ $400/month** total association dues (flag $400–$550/mo as "moderate HOA" for context; exclude > $550/mo unless a listing is otherwise exceptional).

**Price band:** $400,000 – $750,000 list price (for closed sales, use sale price).

**Markets and micro-markets:**

| Metro market (baseline) | Micro-market (focus area) | Geographic proxy |
|---|---|---|
| San Diego, CA (city) | Carmel Valley | ZIP 92130 |
| San Diego, CA (city) | UTC / University City | ZIP 92122 (incl. La Jolla Colony, Costa Verde) |
| Loudoun County, VA | Arcola / Stone Ridge corridor | ZIP 20105 (Aldie/Arcola), Arcola Center, Dulles South |
| Fairfax County, VA | Oakton HS attendance area | Oakton / Vienna ZIPs 22124, 22181, and the portions of 22031/22033 zoned to Oakton HS — verify against FCPS boundary locator when ambiguous |
| Dallas, TX (city + inner suburbs) | — (metro-level only) | City of Dallas; note Plano/Richardson/Frisco spillover where relevant |
| Austin, TX (metro) | — (metro-level only) | City of Austin; note Round Rock/Cedar Park spillover where relevant |

**Rule:** Always report each micro-market *side-by-side* with its parent market (Carmel Valley vs. San Diego city, UTC vs. San Diego city, Arcola vs. Loudoun County, Oakton HS area vs. Fairfax County) so divergence is visible.

---

## 2. Daily Routine (run each weekday morning)

### Step 0 — Load state
- Fetch current state from the public GitHub repo (see §5 for repo layout and §6 for setup):
  - `data/metrics.csv` — running daily time series per area
  - `data/listings.json` — active watchlist with price history
  - `data/rentals.csv` — rental time series per micro-market
  - the most recent file in `digests/daily/`
- Read via `raw.githubusercontent.com/<user>/housing-digest-data/main/<path>` (public repo, no auth needed for reads), or via the Zapier GitHub connector.
- From state, build today's working context: yesterday's metric values (for deltas), the active watchlist (check each watched listing for price/status changes in Step 3), and any "verify tomorrow" items carried over from the last digest.
- If state files are missing or malformed, log it in the digest's Watch Items, proceed with a fresh baseline, and repair the files in Step 8 — never silently overwrite history.

### Step 1 — Rates & macro pulse (1–2 searches)
- Current 30-yr fixed mortgage rate (Mortgage News Daily for daily reads; Freddie Mac PMMS on Thursdays).
- Any rate-moving news in the last 24h (Fed commentary, CPI/jobs prints).

### Step 2 — New listings (per market, filtered to scope)
For each of the 6 areas (4 metros + treat the 4 micro-markets as first-class queries):
- Search Redfin/Zillow/Realtor.com for **townhouse + condo listings, $400K–$750K, listed in the last 1–2 days**.
- Capture per listing: address/neighborhood, type, beds/baths, sqft, list price, $/sqft, HOA fee, year built, days on market, notable remarks (incentives, rate buydowns, "as-is").
- Apply the low-HOA filter; note how many candidates were excluded for high HOA (this itself is a market signal).

### Step 3 — Transactions & status changes
- New **pendings/contingents** and **closed sales** in the last 1–3 days in the same scope.
- For closed sales: sale price vs. list price (list-to-sale ratio), DOM, any concessions if reported.
- **Price cuts:** count and magnitude of reductions in active scope inventory.

### Step 4 — Market metrics snapshot
Track these daily (or use most recent available; many update weekly):
- Active inventory count in scope (price band + type) per area
- Median list $/sqft
- Median DOM
- Months of supply (when available)
- Share of listings with a price cut

### Step 5 — Rental market check (micro-markets)
For Carmel Valley, UTC, Arcola/20105, and the Oakton HS area:
- New rental listings for 2–3BR townhouses/condos; median asking rent and $/sqft rent.
- Compute a rough **rent-to-price ratio** (annual rent ÷ typical scope purchase price) and a naive gross yield; flag changes vs. trailing week.
- Note rental DOM and concessions (free month, etc.) as demand signals.

### Step 6 — Sentiment & news scan
- Local sources: SDAR / San Diego U-T (SD), Bright MLS releases / Northern Virginia mag / WTOP (NoVA), MetroTex / Dallas Morning News (DFW), Unlock MLS (ABoR) / Austin Monitor (Austin).
- Builder activity in Arcola/Dulles South and Dallas/Austin (incentives, new townhouse communities) — incentives often lead resale price cuts.
- Optional: notable Reddit/Bogleheads/local-forum chatter only if it surfaces a concrete, checkable claim.
- Classify each area's same-day sentiment as **Seller-favored / Balanced / Buyer-favored**, with one sentence of justification.

### Step 7 — Daily digest output

Format the daily output as:

```
# Housing Digest — [Date]

## Rates & Macro
[1–3 sentences]

## Market Dashboard
[Table: Area | Active inv. | New listings | New pendings | Closed | Median $/sqft | DOM | % price cuts | Sentiment]
(rows: San Diego city, ▸Carmel Valley, ▸UTC, Loudoun Co., ▸Arcola, Fairfax Co., ▸Oakton HS area, Dallas, Austin)

## Notable New Listings
[Up to 3 per area, one line each: price | type | beds/sqft | HOA | hook]

## Transactions Worth Noting
[Pendings/solds that reveal pricing power or weakness — esp. sale-to-list outliers]

## Micro vs. Metro Divergence
[2–4 sentences: where the focus areas are out- or under-performing their parent markets]

## Rental Pulse
[Rent levels, yield estimates, and any shift, per micro-market]

## Sentiment & News
[Bullets with sources]

## Watch Items
[Listings being tracked for cuts/pending; data anomalies to verify tomorrow]
```

**Daily discipline:** Prefer primary/MLS-fed sources (Redfin Data Center, Zillow Research, association reports) over aggregator blog posts. When daily counts are noisy (micro-markets may have 0–3 events/day), say so plainly rather than over-interpreting. Always date-stamp figures and note which are real-time vs. last published.

### Step 8 — Commit state & deliver
1. **Append, don't rewrite.** Add today's row per area to `data/metrics.csv` and per micro-market to `data/rentals.csv`. Update `data/listings.json`: add new watchlist entries, append price-history events to existing ones, move sold/expired entries to `status: "closed"`/`"delisted"` (keep them for 90 days, then archive).
2. **Save the digest** as `digests/daily/YYYY-MM-DD.md`.
3. **Commit to GitHub** via the Zapier GitHub connector (create/update file actions), one commit per file or a single batched commit, message: `daily: YYYY-MM-DD`.
4. **Mirror to Google Sheets** via the Zapier Google Sheets connector: append today's rows to the `Metrics` and `Rentals` tabs; upsert changed rows in the `Watchlist` tab (match on `listing_id`).
5. **Email the digest** to **jwu9106@outlook.com** via Zapier (use the connected email/Outlook send action). Subject: `Housing Digest — YYYY-MM-DD`. Body: the full daily digest; include a link to the GitHub-archived copy. If the email action fails, retry once, then surface the failure prominently rather than failing silently.

---

## 3. Weekly Digest (run Sunday or Monday morning)

**Load state first:** pull the trailing 7 daily rows from `data/metrics.csv` and `data/rentals.csv`, the full `data/listings.json`, and the week's files from `digests/daily/`. Compute WoW deltas from the stored series — do not reconstruct the week from fresh searches. Run one fresh verification pass only on headline numbers (rates, inventory counts).

```
# Weekly Housing Digest — Week of [date range]

## Executive Summary
[5–8 sentences: the week's single most important shift per metro; rate path; the one
micro-market that diverged most from its parent market.]

## Week-over-Week Dashboard
[Same table as daily, but showing WoW deltas: inventory Δ, new listings total,
pendings total, closeds total, median $/sqft Δ, DOM Δ, % cuts Δ]

## Listing Flow & Absorption
[Per area: new listings vs. pendings ratio for the week — is inventory building or
clearing? Highlight the price sub-band ($400–550K vs. $550–750K) behaving differently.]

## Transaction Review
[Table of the week's most informative closed sales across all areas:
Area | Address/community | Type | Sale price | List-to-sale | DOM | HOA | Takeaway]

## Micro vs. Metro Scorecard
[For each of the 4 focus areas: 2–3 sentences on relative strength, premium/discount
in $/sqft vs. parent market, and whether the gap widened or narrowed this week.]

## Rental Markets
[Weekly median rents, yield table per micro-market, rent vs. own monthly cost at the
current mortgage rate for a representative $550K–$600K purchase with 20% down.]

## Sentiment Trendline
[Did any area shift between Seller/Balanced/Buyer this week? Supporting evidence.
Builder incentive changes in Arcola, Dallas, Austin.]

## Implications & Positioning
[For a buyer: where the week's data suggests leverage or urgency.
For a seller/investor: pricing and timing notes. Frame as analysis, not advice.]

## Next Week Watchlist
[Scheduled data releases (PMMS Thu, association monthlies, CPI/Fed dates),
specific listings under watch, open data questions.]
```

**Weekly commit & delivery:** save as `digests/weekly/YYYY-Wnn.md` and commit (`weekly: YYYY-Wnn`); append a summary row per area to the `Weekly` tab of the Google Sheet; email the full digest to **jwu9106@outlook.com** with subject `Weekly Housing Digest — Week of [date range]`.

---

## 4. State Files & Schemas

All state lives in a single public GitHub repo, suggested name **`housing-digest-data`**:

**Status: this repo is live.** Repo: `Reytheshark/housing-digest-data` (public, branch `main`). Google Sheet: "Housing Digest Dashboard", ID `189906qIJo_KxlUQIrabWORetIMZ7kn9U4MgVXuoMM_Q`. Day 1 baseline (2026-06-12) is committed. Skip §5 — it is historical record of how setup was done, not a to-do list.

```
housing-digest-data/
├── README.md
├── data/
│   ├── metrics.csv        # daily market metrics, one row per area per day
│   ├── rentals.csv        # daily rental metrics, one row per micro-market per day
│   └── listings.json      # watchlist: tracked listings with price/status history
├── digests/
│   ├── daily/2026-06-12.md
│   └── weekly/2026-W24.md
└── archive/listings/      # closed/delisted watchlist entries older than 90 days
```

**`data/metrics.csv`** — columns:

```
date,area,area_type,active_inventory,new_listings,new_pendings,closed_sales,
median_list_ppsf,median_dom,months_supply,pct_price_cuts,hoa_excluded_count,
sentiment,mortgage_rate_30y,notes,source,as_of
```
- `area`: one of `san_diego_city | carmel_valley | utc | loudoun_co | arcola | fairfax_co | oakton_hs | dallas | austin`
- `area_type`: `metro` or `micro`
- `sentiment`: `seller | balanced | buyer`
- Leave fields blank when unavailable that day; never interpolate.

**`data/rentals.csv`** — columns:

```
date,area,new_rental_listings,median_rent_2br,median_rent_3br,median_rent_ppsf,
rental_dom,concessions_noted,gross_yield_est,rent_vs_own_gap,source,as_of
```

**`data/listings.json`** — array of objects:

```json
{
  "listing_id": "redfin-12345678",
  "address": "123 Example Ct",
  "area": "carmel_valley",
  "type": "townhouse",
  "beds": 3, "baths": 2.5, "sqft": 1650, "year_built": 2004,
  "hoa_monthly": 320,
  "status": "active",
  "first_seen": "2026-06-12",
  "original_list_price": 729000,
  "price_history": [
    {"date": "2026-06-12", "event": "listed", "price": 729000},
    {"date": "2026-06-20", "event": "price_cut", "price": 712000}
  ],
  "watch_reason": "high $/sqft for community; expecting cut",
  "url": "https://www.redfin.com/...",
  "last_checked": "2026-06-12"
}
```
`status` ∈ `active | pending | closed | delisted`. On `closed`, add `sale_price`, `sale_date`, `list_to_sale_ratio`.

**Google Sheet (mirror, human-readable)** — suggested name **`Housing Digest Dashboard`**, four tabs:
1. `Metrics` — same columns as `metrics.csv`
2. `Rentals` — same columns as `rentals.csv`
3. `Watchlist` — flattened listings: `listing_id, address, area, type, beds, sqft, hoa, status, first_seen, original_price, current_price, total_cut_pct, dom, watch_reason, url`
4. `Weekly` — one row per area per week: `week, area, inv_delta, new_listings, pendings, closeds, ppsf_delta, dom_delta, sentiment, divergence_note`

GitHub is the system of record; the Sheet is a convenience mirror. If they disagree, the repo wins.

---

## 5. One-Time Setup (COMPLETED 2026-06-12 — reference only)

### A. Create the public GitHub repo
**The repo itself must be created manually** — verified during setup: Zapier's GitHub integration can write files, branches, issues, and PRs into *existing* repos, but has no Create Repository action.
1. On github.com: **New repository** → name `housing-digest-data` → visibility **Public** → initialize with a README.
2. The agent then seeds the data files via the Zapier GitHub "Create or Update File" action: `data/metrics.csv` and `data/rentals.csv` with their header rows (from §4), `data/listings.json` containing `[]`, and `.gitkeep` placeholders in `digests/daily/`, `digests/weekly/`, `archive/listings/`.
3. Note the raw-read base URL: `https://raw.githubusercontent.com/<your-username>/housing-digest-data/main/`.

> Privacy note: the repo is public, so anyone can read it. The data is non-sensitive market data, but don't put personal notes, credentials, or the delivery email address into repo files. If that's a concern, make the repo private and do all reads/writes through the Zapier connector instead of raw URLs.

### B. Create the Google Sheet
1. Via the Zapier Google Sheets connector: enable/run **Create Spreadsheet**, title `Housing Digest Dashboard`.
2. Create the four tabs (`Metrics`, `Rentals`, `Watchlist`, `Weekly`) — Zapier's **Create Worksheet** action, one per tab — and write the header row to each (headers from §4).
3. Record the spreadsheet ID in the repo `README.md` so every future run can find it without searching.
4. Manual alternative: create the sheet at sheets.google.com with the same tabs/headers; the Zapier connector can then append by spreadsheet name or ID.

### C. Email delivery
1. Confirm the Zapier connector has an email-send action enabled (Outlook, Gmail, or Zapier's "Email by Zapier"). Enable one if not.
2. Recipient for both daily and weekly digests: **jwu9106@outlook.com**.
3. Send a one-time test email (`Housing Digest — setup test`) and confirm receipt (check spam/junk on first delivery) before relying on the routine.

### D. First-run baseline
On the first daily run, expect no deltas — record the day's snapshot as the baseline, note "baseline day, no WoW comparisons" in the digest, and begin delta reporting on day 2.

---

## 6. Standing Rules

1. **Comparability first.** Use the same filters (type, price band, HOA cap) everywhere so cross-market comparisons are apples-to-apples; state any unavoidable filter differences.
2. **Small-sample honesty.** Micro-markets like Arcola or the Oakton HS zone may produce single-digit weekly counts — report medians with n, and lean on 4-week rolling views in the weekly digest.
3. **Boundary hygiene.** Oakton HS attendance ≠ ZIP boundaries; when a listing is borderline, check the FCPS boundary locator before counting it. For UTC, exclude La Jolla proper (92037) unless explicitly noted.
4. **Source citation.** Every number in the digest carries its source and as-of date.
5. **Condo-regime townhomes:** classify by structure (attached, multi-level, private entry) but report the HOA structure, since VA/CA condo-regime fees often exceed the cap.
6. **No advice framing.** Present data and analysis; flag that nothing constitutes financial or real-estate advice.
7. **State hygiene.** Append-only for time series; never rewrite past rows except to correct a verified error (note corrections in the commit message). Commit state *before* sending the email, so a failed send never loses data.
8. **Delivery confirmation.** Every run ends with three checkboxes in the digest footer: state committed ✓/✗, Sheet mirrored ✓/✗, email sent to jwu9106@outlook.com ✓/✗ — with the failure reason if any.
