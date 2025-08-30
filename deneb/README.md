# Deneb (Vega-Lite) specs

This folder contains the JSON specs used by the report’s **Deneb** visuals.  
The dashboard reuses a small set of **canonical templates**; KPI cards only differ by formatting
(number vs percent, domains, zero line). Specs contain **no data**.

## Files (templates)

- `templates/kpi_bars_numeric.vl.json` — bar KPI **with a shaded norm band per KPI**; Y is a number (ratios, months).
- `templates/kpi_bars_percent.vl.json` — bar KPI **with a shaded norm band per KPI**; Y is a percentage (ROA/ROE/margins, equity share).
- `templates/trend_pnl.vl.json` — row **4.2** · P&L sparkline (Revenue / EBIT / Net profit).
- `templates/trend_indexed.vl.json` — row **4.3** · Indexed trend sparkline (Assets / Equity / Revenue).
- `templates/legend_status.vl.json` — compact legend (top-right): **below / at_norm / above**.
- `templates/row_labels.vl.json` — vertical row labels (left edge of the grid).

> If you export files under different names, keep the same structure and update the names above.

---

## Norm band (applies to **all 12 KPI cards**)

Each KPI bar visual includes a **shaded norm band** that reflects the acceptable range for that **specific KPI**.  
The band is driven by DAX measures:
- **`[Norm Low]`**, **`[Norm High]`** — thresholds per KPI, sourced from the `ratio_norms` table  
- **`[Ratio Value]`** — the KPI value for the selected year(s)  
- **`[Norm Status]`** and **`[Color Hex]`** — status and palette colour for the bar

This ensures consistent, per-indicator evaluation: the band’s position/width changes by KPI according to its norms.

---

## Data bindings (expected by templates)

**KPI bars (both bar templates)**
- `Year` → X  
- `[Ratio Value]` → Y  
- `[Norm Low]`, `[Norm High]` → shaded norm band (per KPI)  
- `[Color Hex]` → bar colour (palette from DAX)  
- *(optional)* tooltip fields: `[YoY Delta]`, base/reference measures

**Trends (no norm band)**
- `Year` → X  
- `trend_pnl.vl.json`: one of `[Revenue]`, `[EBIT]`, `[Net profit]` → Y  
- `trend_indexed.vl.json`: `[Indexed %]` (or a specific index measure) → Y

**Global UI (no data bindings)**
- `legend_status.vl.json`: static labels **below / at_norm / above** matched to palette colours  
- `row_labels.vl.json`: the four row titles (see mapping below)

---

## Template defaults

- Norm-band opacity: `~0.18` (uniform across KPI cards)  
- Y formatting: percent KPIs → `0%`; numeric KPIs → `0.0` or `0.00`  
- Zero line: visible when the Y domain includes 0 (Working capital may be negative)  
- Colour: taken from DAX `[Color Hex]` (`below / at_norm / above` via `palette`)  
- Axes/labels: compact; no outer border; consistent padding across cards

Minor cosmetic tweaks (bar width, padding, small opacity changes) are styling and are **not** documented here.

---

## KPI → template mapping (matches the dashboard layout)

### Row 1 — Liquidity
| Pos | KPI              | Template                        | Notes                      |
|----:|------------------|----------------------------------|----------------------------|
| 1.1 | Cash ratio       | `kpi_bars_numeric.vl.json`       | numeric, format `0.00`     |
| 1.2 | Current ratio    | `kpi_bars_numeric.vl.json`       | numeric, domain ≈ `[0, 3]` |
| 1.3 | Quick ratio      | `kpi_bars_numeric.vl.json`       | numeric, format `0.00`     |
| 1.4 | Months to repay  | `kpi_bars_numeric.vl.json`       | months, format `0.0`       |

### Row 2 — Financial stability
| Pos | KPI             | Template                        | Notes                                 |
|----:|-----------------|----------------------------------|---------------------------------------|
| 2.1 | Equity ratio    | `kpi_bars_percent.vl.json`       | share of assets                       |
| 2.2 | Debt-to-equity  | `kpi_bars_numeric.vl.json`       | leverage                              |
| 2.3 | Working capital | `kpi_bars_numeric.vl.json`       | allow negatives; zero line emphasised |
| 2.4 | Altman index    | `kpi_bars_numeric.vl.json`       | Z-score bands via norm band           |

### Row 3 — Profitability
| Pos | KPI               | Template                        | Notes  |
|----:|-------------------|----------------------------------|--------|
| 3.1 | Return on assets  | `kpi_bars_percent.vl.json`       | ROA    |
| 3.2 | Return on equity  | `kpi_bars_percent.vl.json`       | ROE    |
| 3.3 | Net profit margin | `kpi_bars_percent.vl.json`       | margin |
| 3.4 | EBITDA margin     | `kpi_bars_percent.vl.json`       | margin |

### Row 4 — Structure & trends
| Pos | Visual                          | Spec                              | Notes                                    |
|----:|---------------------------------|-----------------------------------|------------------------------------------|
| 4.1 | Structure of current assets     | *(native PBI or custom)*          | cash / inventories / receivables         |
| 4.2 | P&L trend (Revenue / EBIT / NP) | `templates/trend_pnl.vl.json`     | thin sparkline, minimal axes             |
| 4.3 | Indexed trend (Assets/Equity/Rev) | `templates/trend_indexed.vl.json` | base year = 100 (handled in DAX)         |
| 4.4 | Structure of liabilities        | *(native PBI or custom)*          | current / non-current / equity           |

> **Row label strings** used by `row_labels.vl.json` (top → bottom):  
> `Liquidity`, `Financial stability`, `Profitability`, `Structure & trends`.

---

## Export / reuse

**Export from Power BI:** select a Deneb visual → **… → Export Spec** → save JSON → place it under `deneb/templates/`.  
**Reuse in a new report:** add a Deneb visual → **Edit** → paste the template JSON → bind fields as above (for KPI/trend templates).  
Legend and row labels are static specs: insert, size, and position them (no data bindings required).
