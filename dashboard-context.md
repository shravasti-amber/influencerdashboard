# Amber Influencer Marketing Dashboard — Full Project Context

## What it is
A single-file HTML dashboard (`index.html`) for Amber's influencer marketing team. No backend, no database, no build step — everything runs in the browser. Upload an xlsx or csv, get charts. All data stays on your laptop.

- **Deployed at:** https://influencerdashboard-kappa.vercel.app/
- **GitHub repo:** shravasti-amber/influencerdashboard
- **Local dev:** `python3 -m http.server 8765` → http://localhost:8765/
- **Working directory:** `/Users/Amber_User/Downloads/dashboardinfluencer/`

## Tech Stack (all CDN, no install)
- Tailwind CSS — styling
- Chart.js + chartjs-plugin-annotation — charts and target lines
- SheetJS (xlsx) — multi-sheet xlsx parsing
- PapaParse — CSV parsing
- Vanilla JS — everything else

---

## Upload Flow
Two separate upload buttons in the header:
- **Upload Prospects** (indigo) — feeds only the Prospects tab
- **Upload Growth Tracker** (fuchsia) — feeds only the Growth Tracker tab
- Files are never committed to GitHub (gitignored)
- Auto-detection via `sheetType()` reads headers to identify sheet type

---

## Tab 1: Prospects
Powered by the Master prospect sheet from the existing xlsx workbook (~6,100 rows).

### KPIs
Total prospects, Onboarded · Live, Conversion rate `(onboarded + WIP) / total`, Active follow-ups, Lost, POCs active

### Charts
- Pipeline funnel (by status)
- Outreach over time (bar + line)
- Performance by POC (stacked bar: Onboarded / WIP / Follow-ups / Contacted / Lost, sorted alphabetically)
- Platform mix donut (outreach)
- Channel-wise Onboarded (table: Platform | Onboarded count | Grand Total)
- Top destinations bar
- Top source countries bar
- Source → Onboarded (stacked bar, toggle between By Country / By Region)
- Month-over-month grouped bar (current month vs previous month: Outreach, Onboarded, WIP, Follow-ups, Lost)
- Conversion rate by POC (bar)
- Lost reasons bar

### Weekly Progress Table (bottom of page)
- Shows each POC's reach-outs broken down by calendar week of the selected month
- Week 1: 1–7, Week 2: 8–15, Week 3: 16–23, Week 4: 24–end of month
- Columns: POC | Week 1 | Week 2 | Week 3 | Week 4 | Total Reachouts | Onboarded
- Defaults to current month (June 2026); warning shown if multiple months selected
- Team total footer row
- Palash (Manager) excluded

### Filters
POC, Month (Jan 2026 onwards only), Platform, Destination, Source Country, Status — all multi-select, click any chart bar/slice to filter

### Auto-corrections Applied on Parse (source file untouched)
- Excel date drift → +30s nudge
- Month misspellings → fuzzy match
- Typo years (e.g. "206") → corrected to current year
- POC names → trimmed + title-cased
- Platforms: Tiktok/TikTok, Youtube/YouTube merged
- Destinations: Aus/Australia, UK variants merged
- Statuses: Onboarded variants → "Onboarded — Live"; Lost variants → "Lost"
- Text dates parsed as dd/mm/yyyy (Indian format)

---

## Tab 2: Growth Tracker
Powered by a separate Growth Tracker CSV/xlsx uploaded independently.

### Columns in the Growth Tracker Sheet
Date, Month (number), POC name, Activity (video/reel/tiktok/shorts), Partner name, UTM Links, Budget (INR), Content Type (Study Abroad/Scholarship/Amber Fest), Channel (YouTube/Instagram/TikTok), Source Country, Destination Country, Broader region by Source Country, Total views, Traffic, Leads, Bookforms, Bookings, Language, Content Usage Rights, Paid/affiliate, New onboarding/Existing, Ads boost

### KPIs
Activities, Total spend, Views + CPV, Traffic, Leads + CPL, Bookforms + CPB, Bookings

### Charts
- Funnel: Views → Traffic → Leads → Bookforms → Bookings
- Paid vs Affiliate side-by-side cards (spend, views, leads, bookings, CPL)
- Platform performance bar (traffic + leads + spend, tooltip shows % of total traffic for traffic bars)
- Cost efficiency by channel table (CPV / CPL / CPB)
- POC performance — traffic & leads (sorted by traffic)
- Top partners by leads
- Source → leads bar
- Destination → bookings bar

### Video Progress Tracker (bottom of page)
- Horizontal stacked bar chart per POC showing videos done week-by-week (Week 1–4, light → dark indigo)
- Two vertical dashed target lines: red at 20 (Interns), purple at 30 (Full-time + Royce)
- Chart POC order: Royce, Abhishek, Kaamil, Deeksha, Ishika, Aashray, Aarya, Shravasti, Akksheda, Divyanshu, Ishita, Pratiksha, Pooja, Shravani, Devansh, Anisha, Janhavi
- Progress table below: POC | Week 1 | Week 2 | Week 3 | Week 4 | Done | Target | % Done | Status
- Status labels: <25% "Gotta push 🔴", 50%+ "Good going! 🟡", 75%+ "Almost there! 🟠", 100% "All done! ✅"
- Defaults to current month; warning if multiple months selected
- Counting rule: 1 row = 1 video, only when Activity column is filled (blank = cross-posted, excluded)
- Palash (Manager) excluded

### Filters
POC, Month, Channel, Destination, Source Country, Paid/Affiliate, Content Type

---

## Role Classification (hardcoded)

| Role | People | Videos/month target |
|---|---|---|
| Interns | Ishika, Aashray, Aarya, Shravasti, Akksheda, Divyanshu, Ishita, Pratiksha, Pooja, Shravani, Devansh, Anisha, Janhavi | 20 |
| Full-time | Deeksha, Abhishek, Kaamil | 30 |
| Assoc. Manager | Royce | 30 |
| Manager | Palash | excluded from tracking |

### Per-POC Monthly Overrides
- Shravasti: June 2026 → 25 videos

### Prospecting Targets (for context, not yet in dashboard)
- Interns: 120 reach-outs/month, 8 onboards/month
- Full-time: 72 reach-outs/month, 6 onboards/month
- Assoc. Manager: 32 reach-outs/month, 3 onboards/month

---

## Known Data Quirks Handled
- 4 rows in video data with date "Feburuary 19, 2027" (misspelled + wrong year) — auto-corrected
- 11 rows in prospects with year "206" — auto-corrected to 2026
- SheetJS returns Excel date cells ~10 seconds short of midnight — nudged +30s
- Shravasti + Feb 2026 previously returning 0 rows due to date drift — fixed

---

## Deliberately Excluded
- Data tables (removed — visualisation only)
- Export CSV buttons (removed)
- Targets vs Actuals tab (removed at user request)
- Quality column from growth tracker (excluded for now)

---

## Roadmap / Next Steps
- More charts on the Growth Tracker tab
- Join prospects ↔ growth tracker ↔ bookings data
- Auto-refresh from Google Drive (needs small backend)
- Mobile polish
