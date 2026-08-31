# Pharmacy Expiry Shelf Check (P02)

**Team ID:** LSH26-T029
**Problem ID:** P02 — Pharmacy Expiry Shelf Check
**Live URL:** (https://hack-craft-ebon.vercel.app/)


## Proof each requirement is met

| # | Requirement | Where / how |
|---|---|---|
| 1 | Stock list, ≥ 40 medicines, mixed expiry states | `data/stock.json` / embedded in `index.html` — 44 items: 10 already expired, 6 expiring ≤ 30 days, 8 expiring ≤ 90 days, 17 safe (> 90 days, several > a year out) |
| 2 | Dashboard split into 4 groups with counts | The four tiles at the top of the page (Expired / Expiring ≤ 30d / Expiring ≤ 90d / Safe), each showing a live count; `bucketOf()` in `index.html` computes the group from `new Date()` on every render — nothing is pre-labelled in the data |
| 3 | Mark item as returned → separate list | "Mark returned" button on every active row; `markReturned()` sets `returned: true` and the item moves into the "Returned to distributor" section, out of the active table |
| 4 | Total BDT value in Expired and Expiring-soon groups | The two headline numbers at the top of the page (`heroExpiredValue`, `heroSoonValue`); computed as Σ(quantity × `unit_price_bdt`) per ruling R-27, with "expiring soon" scoped to 0–30 days inclusive per ruling R-04 |

Rulings followed explicitly:
- **R-27** — `unit_price_bdt` is the purchase price; value at risk =
  quantity × unit price.
- **R-04** — "Expiring soon" = the 0–30-day group only, inclusive; expired
  items are a separate group and are not folded into the "soon" total.
- **R-24** — a returned item leaves both the active counts and the active
  value totals (verified: `renderStats()` filters on `!m.returned` before
  bucketing or summing).

**Bonus features implemented (all three):**
- Search by medicine name or company, plus one-click bucket filter chips.
- A 6-month value-at-risk bar chart (hover for exact BDT + item count per
  month), built as inline SVG — no charting library.
- Quick-add form with shelf-life presets (6 months / 1 year / 18 months /
  2 years); 18 months is pre-filled as the common default and remains
  editable.

## Major decisions

- **Mutually-exclusive buckets.** Expired / ≤30d / ≤90d / Safe are treated
  as non-overlapping and exhaustive over active stock (0–30, then 31–90,
  then 90+), so the four tile counts always sum to the active item count.
- **Live date, not a frozen "today."** Bucketing reads the browser's real
  current date on every render rather than a value baked into the sample
  data, per the "must be worked out from today's date" constraint — so the
  dashboard keeps re-sorting itself correctly on any day it's opened.
- **Sample data sourced from the official fixture.** Rather than inventing
  numbers, the 44-item list is case `PUB-12` from the problem's published
  sample-data set, whose `today` field happens to equal the day this was
  built — reshaped into this project's field names in `data/stock.json`.
- **Colorblind-checked palette.** The four status colors (and the brand
  accent) were validated against an OKLCH lightness/chroma/contrast and
  CVD-simulation check rather than picked by eye, for both light and dark
  mode.

## Approach and contributions

**Approach:** the team first read the problem statement and the published
rulings (R-27, R-04, R-24) together to lock down the exact bucket
definitions and value formula, then worked from the organisers' own sample
fixture instead of inventing numbers, before moving to the dashboard build
and a final check of every requirement and ruling against the running app.

**Contributions:**
- **Rahul — Team Leader:** set the overall approach and architecture for
  the dashboard (data model, how the four buckets and value-at-risk map to
  the rulings, and the UI direction), and drove the build.
- **Hemayet:** problem-solving and analytical logic — working through the
  bucket definitions and the value-at-risk math against rulings R-27/R-04,
  and checking the requirement-by-requirement proof in this README.
- **Manik:** the dashboard's data-visualization implementation — the
  status tiles/pills and the 6-month value-at-risk chart.

