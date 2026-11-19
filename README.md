# Hero Cleaners — Residential Customer Dashboard

Live dashboard: **https://camsmith126.github.io/hero-cleaners-residential-dashboard/**

This repository hosts **only the generated dashboard artifact** (`index.html`).
It contains no source code.

## How it works

The dashboard is produced by the **Residential Agent** pipeline (a separate
local project). Every morning a cron job runs the pipeline, which:

1. Pulls future scheduled jobs from the HouseCall Pro API
2. Reads the residential workbook (Daily Report + Finance Report)
3. Combines with historical data from the Analytics Agent database
4. Classifies customer states and writes `data/dashboard.html`
5. Copies that file here as `index.html` and pushes to this repo

GitHub Pages serves the pushed `index.html`. The page is fully self-contained
(Chart.js + Grid.js load from CDN) and refreshes its data on each daily push.

## Update cadence

Auto-deployed once daily, shortly after the 6:00 AM Mountain Time pipeline run.
The timestamp in the dashboard header shows when the underlying data was last
refreshed.

## Editing

Do not edit `index.html` by hand — it is overwritten on every deploy. Changes
to the dashboard are made in the Residential Agent project's `build_artifact.py`.

## How to update the 2027 Plan tab each month

The **2027 Plan** tab compares Jaylee's 2027 plan with actual results. Most of
it fills itself; a few numbers are typed in once a month, after the monthly
cleaning P&L is closed, into `data/plan2027_actuals.json` in the Residential
Agent project (one object per month). Then run `bash run_intraday.sh`.

Filled automatically at build time (do not type these):

| Field | Source |
|---|---|
| `mrr_end`, `recurring_customers` | frozen on the 1st of the next month from the dashboard's live MRR and roster |
| `churn_pct` | recurring customers lost in the month ÷ roster at month start |
| `new_first_time`, `conversion_pct` | Jaylee's pipeline log (first cleans logged; converted ÷ qualified) |
| recurring / one-time revenue | completed cleans on this dashboard |

Typed in each month:

| Field | Where it comes from |
|---|---|
| `total_rev` | monthly cleaning P&L, cash collected |
| `cleaner_pay_field` | Gusto: cleaner wages for cleaning hours, loaded (incl. payroll tax) |
| `cleaner_pay_mgmt` | Gusto: management hours paid to cleaners (e.g. Halee), loaded |
| `labor_hours` | total cleaner hours (for revenue per labor hour) |
| `supplies` | monthly P&L |
| `overhead` | cleaning's share of overhead, monthly P&L |
| `csr` | CSR pay, loaded |
| `gm_base_loaded` | GM base pay, loaded |
| `lsa_spend` | Google Ads / Local Services Ads charges for the month |
| `lsa_new_customers` | first-time customers attributed to LSA |

Until `total_rev` is entered, the tab uses the dashboard's completed-visit
revenue and says so. The current month is shown but not judged until its P&L
is in.

**Pay data is never published here.** This public page leaves out the raise
tracker, profit share, and GM/CSR pay lines (they are folded into
"management & overhead"). To see them, build locally with
`python build_artifact.py --with-pay`, which writes
`data/dashboard_private.html` and is never deployed.
