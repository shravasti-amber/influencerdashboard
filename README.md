# Influencer Marketing Dashboard

Single-file dashboard for Amber's influencer marketing team. Replaces fragile Excel pivot tables — upload a workbook, get instant charts and filters.

## What it does

- **Upload** the weekly `.xlsx` (multi-sheet) or any single `.csv`. Everything stays in your browser — no server, no upload.
- Auto-detects the **Master prospect**, **video live data**, and **Target Sheet** tabs and routes each to its own dashboard view.
- **Prospects view** — pipeline funnel, outreach over time, per-POC performance, conversion rates, source/destination breakdowns, MoM growth, lost reasons.
- **Videos view** — Views → Traffic → Leads → Bookforms → Bookings funnel, CPV / CPL / CPB, channel performance, paid vs affiliate comparison, top partners.
- **Targets vs Actuals** — per-POC monthly reach-out (120/mo) and onboarding (4/mo) attainment.
- Click any chart bar / slice to filter — combine clicks across charts to slice mid-meeting.
- Search, paginate, and export filtered CSV.

## How to run it

```bash
# clone
git clone https://github.com/kaustubhbamber/influencerdashboard.git
cd influencerdashboard

# serve it locally (data files are git-ignored, so place your .xlsx in this folder)
python3 -m http.server 8765
# then open http://localhost:8765/dashboard.html
```

Or just **double-click `dashboard.html`** to open in a browser and use the **Upload** button to load any workbook. (The "Load current workbook" auto-loader only works over `http://`, not `file://`, due to browser security.)

## Why git-ignore the data

The workbook contains influencer PII (emails, names, channel links). The `.gitignore` excludes `*.xlsx` / `*.csv` by default. Keep data files local; load them through the Upload button when demoing.

## Tech

- **Tailwind CSS** (CDN) for styling
- **Chart.js** (CDN) for charts
- **PapaParse** (CDN) for CSV parsing
- **SheetJS** (CDN) for `.xlsx` parsing
- Pure HTML + vanilla JS — no build step, no backend
