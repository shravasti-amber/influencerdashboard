# Amber Influencer Marketing Dashboard — Session 1 Context

## 1. The Brief

**Who:** Influencer marketing team at Amber (student accommodation). Reaches out to student influencers in source countries (India, Pakistan, Malaysia, UK, US, Germany, etc.) and onboards them to drive bookings in destination countries (UK, USA, Germany, Ireland, Canada, Australia).

**The pain:** Team data lives across many Excel sheets. Pivot tables are manual, formula-heavy, and break when an upstream cell changes. Rebuilding them every Monday is meeting friction.

**The goal:** Upload a workbook → get a dashboard. Demo to team on 2026-05-22.

**Background:** Has built Grafana + Postgres dashboards before — technical explanations skip the basics.

---

## 2. What Got Built

A single-file HTML dashboard — no backend, no DB, no build step. Drop the xlsx, get charts.

### Tech Stack (all via CDN)
- Tailwind CSS — styling
- Chart.js — charts
- PapaParse — CSV parsing
- SheetJS (xlsx) — multi-sheet xlsx parsing
- Vanilla JS — state, filters, rendering

### Prospects Tab (from Master prospect sheet)
- 6 KPI cards: Total prospects, Onboarded · Live, Conversion rate, Active follow-ups, Lost, POCs active
- Pipeline funnel (Contacted → Replied → FU 1/2/3 → In talks → Onboarded · Live → Lost)
- Outreach over time (bar = outreach, line = onboards)
- Performance by POC — stacked bar, click to filter
- Platform mix donut, top destinations, top source countries
- Source country → onboarded (stacked, sorted by onboards)
- MoM growth % (outreach & onboards)
- Conversion rate by POC (min 30 prospects)
- Lost reasons bar
- Searchable paginated data table

### Videos Live Tab (from video live data sheet)
- 7 KPI cards: Videos live, Total spend, Views (+CPV), Traffic, Leads (+CPL), Bookforms (+CPB), Bookings
- Views → Traffic → Leads → Bookforms → Bookings funnel with drop-off %
- Paid vs Affiliate side-by-side cards
- Trend over time (multi-line: views, traffic, leads, bookings)
- Channel performance — leads, bookings, spend with CPL annotations
- Channel efficiency table (CPV / CPL / CPB by channel)
- POC performance, Top partners by leads
- Source → leads, Destination → leads
- Searchable paginated data table

### Cross-cutting
- 6 multi-select filter dropdowns per tab (POC, Month, Platform/Channel, Destination, Source, Status/Paid-Affiliate)
- Click any chart bar/slice to filter — combine clicks to slice live in meeting
- Reset filters, Export filtered CSV
- Drag-drop or click-to-upload .xlsx / .csv
- All processing in browser — no data leaves your laptop

---

## 3. URLs & Files

| Thing | Where |
|---|---|
| GitHub repo | shravasti-amber/influencerdashboard (moved from kaustubhbamber/influencerdashboard) |
| Deployed dashboard | https://influencerdashboard-kappa.vercel.app/ |
| Local dev | `python3 -m http.server 8765` → http://localhost:8765/ |
| Working dir | `/Users/Amber_User/Downloads/dashboardinfluencer/` |

**Tracked files:**
- `index.html` — the entire dashboard (was dashboard.html, renamed for clean Vercel root URL)
- `README.md`
- `.gitignore` — excludes *.xlsx, *.csv (influencer PII never gets committed)
- `vercel.json` — security headers (no build config needed)

**Local-only (gitignored):**
- `Interns Influencer _prospects Tracker .xlsx` — 14 MB master workbook (58 sheets)
- `Interns Influencer _prospects Tracker - Master prospect.csv` — exported CSV

---

## 4. Data Observations

**Master prospect (6,114 rows):**
- 17 POCs — top 3: Aashray (1,195), Deeksha (1,020), Ishika (624)
- Reach-out dates: 4,708 in 2026; 1,079 in 2025; 11 rows with year "206" (typo); 3 rows in 2024

**Video live data (1,135 active rows out of 11,581):**
- Total: 12M views, 47K traffic, 1,276 leads, 161 bookforms, 15 bookings, ₹39.5L spend
- Channels: Instagram (463), YouTube (350), TikTok (302), Facebook (11), X (8)
- Paid vs Affiliate: 487 paid / 446 affiliate
- 4 rows with date "Feburuary 19,2027" — typo (misspelled month + wrong year)

**Sheets in the workbook (58 total). Useful ones:**
- Master prospect ✅ powering prospects view
- video live data ✅ powering videos view
- Leaderboard — MoM matrix, conversion rank, "🔥 Top Performer" tags (re-derived dynamically)
- Target Sheet — per-POC targets (120 reach-outs/mo, 4 onboards/mo). Built a Targets view, then removed on request.
- 17 per-POC tabs, several Pivot Table sheets, Campaign list, Source country master, etc.

---

## 5. Bugs Found & Fixed

| Bug | Cause | Fix |
|---|---|---|
| Months sorted alphabetically, not chronologically | `new Date("February 2026 1")` parsed unreliably | `monthSortKey` parses "Month YYYY" manually using a month-number map |
| Charts not filterable mid-meeting | No interactivity | Added `chartClickFilter` — click any bar/slice toggles that value in the filter |
| X-axis labels showed full Date strings | Master prospect xlsx Month column has Date cells; `norm(Date)` stringifies via `toString()` | Added `deriveMonth` helper that handles Date/string/number/empty consistently |
| Feb 2026 missing from filter, Shravasti+May 2026 returned 0 rows | SheetJS returns Excel dates ~10s short of midnight — Feb 1 became Jan 31 23:59:50 | Added `fixXlsxDate` — nudges +30s to round to correct day |
| "Feb 2027" appearing in Videos tab | 4 rows had typed string "Feburuary 19,2027" — V8 parses it as Feb 19 2027 | Added `correctTypoYear` — remaps year outside [2024, current year]; `fixMonthSpelling` fuzzy-matches misspelled months |
| "Year 206" rows polluting Month filter | Typed without leading "20" | Caught by `correctTypoYear` — remaps to 2026 |
| Work email in commit history | Used product-tools@amberstudent.com initially | Rewrote history via `git filter-branch` → all commits now kaustubhbamber@users.noreply.github.com. Force-pushed. |

**Auto-corrections active during visualization (source xlsx untouched):**
- 10-second xlsx date drift → +30s
- Month misspellings → fuzzy prefix match to full name
- Year < 2024 or > current year → remap to current year (or prev year if that's in future)
- POC names: trim + title-case (merges "Ishika " / " Anisha" / "royce")
- Platforms: Tiktok/TikTok, Youtube/YouTube merged
- Destinations: Aus/Australia, UK Doms variants merged
- Statuses: Onboarded/Onboarded-Live/Confirmed/Contract Sent all → "Onboarded — Live"; Lost/Not interested/Ghosted → "Lost"

---

## 6. Commits on Main

```
dd7cf32 Auto-correct sheet typos during visualization
c4e8200 Filter future-dated typo rows from month aggregations
064643d Guard against typo years in date cells
bce6dc1 Fix xlsx date drift causing wrong month labels
8e51025 Prep for Vercel deploy
8773498 Remove Targets vs Actuals tab
a90a62d Fix month derivation when source is xlsx (Date cells, empty Month col)
34c3c05 Add influencer marketing dashboard
```

---

## 7. Decisions Made

| Question | Decision | Why |
|---|---|---|
| HTML single-file vs framework | Single HTML | No build step, no install, easy to share, parses xlsx in browser |
| GitHub Pages vs Vercel | Vercel | Auto-deploys on push, faster than GH Pages |
| Include data files in deploy? | No, gitignored | Influencer PII (emails, names) shouldn't sit in a public repo |
| 3 tabs vs 2 tabs | Removed Targets tab | User request |
| Typo rows: filter or auto-correct? | Auto-correct | "Can be corrected in the dashboard while visualizing" |
| Mobile responsiveness | Partial — works on tablet, rough on phone | Demoing on laptop; offered to polish if needed |

---

## 8. Roadmap (from Session 1)

1. **Targets vs Actuals** — bring 120 reach-outs/mo and 4 onboards/mo targets back as a third tab with per-POC per-month attainment bars (already coded once, can re-add)
2. **Join prospects ↔ videos ↔ bookings** — trace each booking back to influencer / POC / campaign
3. **Auto-refresh from Drive** — nightly sync the workbook from Google Drive (needs small backend; Vercel cron + Drive API)
4. **Self-serve chart builder** — move to Metabase on a Postgres warehouse (Grafana stack pattern, but BI-friendly UI)
5. **Mobile polish** — header wrap, hardcoded grid-cols-2 → responsive, smaller chart heights below md: breakpoint
