# Integra LLC — Financial health dashboard (Power BI)

A product-style, reproducible **financial health dashboard** for a mid-sized company (2021–2023).  
The report tracks **12 core KPIs** with YoY deltas, target norms, and risk flags, plus structural and trend views (assets, liabilities, and P&L).

**Live demo:** [irinatok11.github.io/fin-health-power-bi](https://irinatok11.github.io/fin-health-power-bi/) ·

---

## Table of contents
- [Overview](#overview)
- [Who this is for](#who-this-is-for)
- [Key features](#key-features)
- [Design variants (two pages)](#design-variants-two-pages)
- [Pages](#pages)
- [Data model (at a glance)](#data-model-at-a-glance)
- [KPIs tracked](#kpis-tracked)
- [Per-KPI trend badge](#per-kpi-trend-badge)
- [Screenshots](#screenshots)
- [Project structure](#project-structure)
- [Getting started](#getting-started)
- [Methodology (how it works)](#methodology-how-it-works)
- [Data and privacy](#data--privacy)
- [Cross-workflow usage](#cross-workflow-usage)
- [Roadmap](#roadmap)
- [License](#license)
- [Contact](#contact)
---

## Overview
**Goal:** provide a clear, decision-ready view of the company’s financial condition across **12 ratios** with YoY deltas, norms, and flags.  
**Tech stack:** Power BI Desktop · DAX (comma arguments) · Deneb (Vega-Lite JSON) · Excel.

---

## Who this is for
Executive leadership and finance leads; licensed insolvency practitioners (administrators, liquidators, CVA supervisors); and restructuring and turnaround advisers.


## Key features
- **One-screen health check** with traffic-light logic and concise commentary.
- **Norm bands per KPI** (soft ranges with a neutral zone) used on **all 12 KPI cards**.
- **YoY deltas** (absolute/relative), directional arrows, formatted tooltips.
- **Per-KPI trend badge** (top-right of each card): arrow + colour by current norm status.
- **Consistent visual language**: theme, typography, colour scale for “above / at / below norm”.
- **Reproducible setup**: versioned DAX, Deneb specs, and documentation.

---

## Design variants (two pages)
- **Page 1 — v1 (rounded cards):** live, presentation-style dashboard with rounded corners and soft background (web look).
- **Page 2 — v2 (flat cards, print-friendly):** same data and measures, but a flat visual style optimised for clean Word screenshots.  
  
> v1 and v2 use the **same** numbers; v2 only changes styling to paste visuals cleanly into Word.

---


## Pages
- **Financial health** — main dashboard (12 KPIs with norms, deltas and trends)
- **3.2 – Word export** — print‑ready report section (liquidity narrative)

---

## Data model (at a glance)
- **Tables:**  
  `financials_long` (fact: yearly values) · `ratios` (KPI aggregates & measures) · `years` (helper) · `palette` (theme) · `Sections` (UI helpers).
- **Relationships:** star-style around `financials_long[Year]` (one-to-many with `years`).
- **Measures location:** stored under table **ratios** and versioned in [`/dax`](./dax).
- **Definitions & norms:** summarized in [`docs/methodology.html`](https://irinatok11.github.io/fin-health-power-bi/methodology.html).

---

## KPIs tracked
### 1) Liquidity
- Cash ratio
- Current ratio
- Quick ratio
- Months to repay (liquidity coverage in months)

### 2) Financial stability
- Equity ratio (Autonomy)
- Debt to equity
- Working capital (absolute)
- Altman Index

### 3) Profitability and returns
- Return on assets (ROA)
- Return on equity (ROE)
- Net profit margin
- EBITDA margin

### 4) Structure and trends
- Structure of current assets (cash, inventories, trade receivables)
- P&L trends (Revenue, EBIT, Net Profit)
- Indexed trends (Assets, Equity, Revenue)
- Structure of liabilities (current liabilities, non-current liabilities, equity)

---

## Per-KPI trend badge
Every KPI card includes a small **trend badge** in the top-right corner.  
The badge communicates both **direction** (YoY change) and **quality vs norm** (colour):

- **Arrow:** ▲ positive YoY change, ▼ negative, ● no change.  
- **Colour:** follows the KPI’s **current norm status** (`below / at_norm / above` → colour hex).  
  So you see at a glance: *is it improving and is it within norm?*

**Visual type:** native Power BI **Card**.

**Measures used (see `dax/`):**
- `YoY Δ` / `YoY Delta %` — latest year vs previous year (numeric or %)
- `Trend Arrow` — ▲ / ▼ / ● based on the sign of `YoY Δ`
- `Trend Chip Text` — formatted label (e.g., `▲ +2.1` or `▼ −13%`)
- `Norm Status` → `Color Hex` — colour taken from the KPI’s current status  
- (optional) blank-handling to hide the badge if the previous year is missing

---

## Screenshots
**Page 1 — v1 (rounded cards)**  
![Dashboard v1](docs/cover.png)

**Page 2 — v2 (flat cards, print-friendly)**  
![Dashboard v2](docs/cover2.png)

---

## Project structure
~~~text
fin-health-power-bi
├─ data/                     # sample/anonymised Excel (e.g., integra_financial_analysis.xlsx)
├─ dax/                      # DAX measures (one file per measure or grouped)
├─ deneb/                    # Vega-Lite specs for Deneb visuals (reproducible JSON)
│  ├─ kpi_bars_numeric.vl.json
│  ├─ kpi_bars_percent.vl.json
│  ├─ legend_status.vl.json
│  ├─ row_labels.vl.json
│  ├─ trend_indexed.vl.json
│  └─ trend_pnl.vl.json
├─ docs/                     # GitHub Pages (live site content)
├─ _config.yml               # Pages config (e.g., title, baseurl)
├─ assets/                   # Static assets used by the pages below
│  └─ css/
│     └─ portfolio.css       # Header styles + Header styles and layout + layout
├─ cover.png                 # Social/OG image (primary)
├─ cover2.png                # Alternate/thumbnail image
├─ index.html                # Landing page for the portfolio (public)
└─ methodology.html          # Methods/notes page for the portfolio
├─ fin-health.pbix           # Power BI report (root)
├─ LICENSE
└─ README.md                 # this file
~~~

---

## Getting started

### Prerequisites
- Power BI Desktop (tested on 2.146.705.0 64 (August 2025), Windows).
- Optional: Deneb custom visual (Marketplace → “Deneb”).

### Open the report
1. `git clone https://github.com/IrinaTok11/fin-health-power-bi`
2. Open **fin-health.pbix** from the repo root.
3. If prompted, set the Excel path to `data/integra_financial_analysis.xlsx`.
4. **Refresh** to recalculate all measures and visuals.

---

## Methodology (how it works)
- **Norm bands.** Each KPI has a soft target range (`Norm Low` … `Norm High`). In‑band is neutral; out‑of‑band is flagged.
- **Directionality.** Each KPI states whether “higher is better” or “lower is better”; statuses are bucketed as `above` / `at_norm` / `below`.
- **Status colour scale.** A single status colour (hex) is used across badges, legend dots and bars to encode the current norm status.
- **YoY deltas.** Measures compute absolute and relative change; formatted strings show ▲/▼ arrows; blanks are handled defensively.
- **DAX style.** Comma‑separated arguments with consistent naming (`grp__KPI__SubKPI__MeasureName`).
- **Deneb.** Clean Vega‑Lite JSON (no `//` or `/* */`). Specs are versioned in `/deneb`.

See the **Methodology** page on the portfolio site for full modelling and visual rules:  
**https://irinatok11.github.io/fin-health-power-bi/methodology.html**


## Data and privacy
- Ships with **anonymised/sample** data (`data/`).
- Replace sample files locally if needed; keep raw identifiers and sensitive fields **out of version control**.

---

## Cross-project usage
- **Page 2 — v2 (flat)** provides clean screenshots for the Word section **“3.2 Analysis of liquidity ratios”** generated by the companion **Python** project.  
  Python portfolio: **live** https://irinatok11.github.io/fin-health-python/ · **repo** https://github.com/IrinaTok11/fin-health-python

---

## Roadmap
- Sector benchmarks (peer table, quartiles).
- DuPont decomposition of ROE.
- Scenario & sensitivity (revenue growth, COGS, WACC).
- Export: **reports/Executive_Summary.pdf** (1–2 pages for leadership).

---

## License
This project is available under the **MIT License**. See [LICENSE](LICENSE).

---

## Contact
**IRINA TOKMIANINA** — Financial/BI Analyst  
LinkedIn: [linkedin.com/in/tokmianina](https://www.linkedin.com/in/tokmianina/) · Email: <irinatokmianina@gmail.com>
