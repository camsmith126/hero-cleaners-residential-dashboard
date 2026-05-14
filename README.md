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
