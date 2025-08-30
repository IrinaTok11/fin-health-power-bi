# DAX measures

This folder contains the full set of measures exported from the Power BI model (read-only; the PBIX remains the source of truth).

- **File:** `measures.dax` (read-only listing for reviewers)
- **Scope:** all KPIs used across the dashboard (liquidity, financial stability, profitability, structure & trends)
- **Palette logic:** statuses `below` | `at_norm` | `above` are mapped to hex colours via the `palette` table

## What to look for

**Norm bands and colour**
- `Norm Low`, `Norm High` — thresholds per KPI (from `ratio_norms`)
- `Ratio Value` — the current KPI in context
- `Norm Status` — returns `below` | `at_norm` | `above`
- `Colour Hex` — looks up the hex colour by status

**Trends and deltas**
- `Delta vs Base`, `Indexed %`, `YoY Δ` (chip/arrow helpers included)
- Base/index measures for Assets, Equity, Revenue

**Business KPIs**
- Liquidity: Cash ratio, Current ratio, Quick ratio, Months to repay
- financial stability: Equity ratio, Debt-to-equity, Working capital (or WCTA, if applicable), Altman index
- Profitability: ROA, ROE, Net profit margin, EBITDA margin

> The full source lives in `measures.dax`. This README shows a brief overview to keep the repository browsable.

## How this file is generated

Regenerate the listing whenever the model changes.

### A) Tabular Editor 2 (script; recommended)
1. Open the PBIX → **External Tools → Tabular Editor** → **C# Script**.  
2. Run the script below (adjust the output path); it creates `measures.dax`.

```csharp
// Export all measures to C:\Projects\measures.dax
var path = @"C:\Projects\measures.dax";
var sb = new System.Text.StringBuilder();
foreach (var m in Model.AllMeasures.OrderBy(x => x.Table.Name).ThenBy(x => x.Name))
{
    sb.AppendLine("-- " + m.Table.Name + "[" + m.Name + "]");
    sb.AppendLine(m.Name + " = " + m.Expression);
    sb.AppendLine();
}
System.IO.File.WriteAllText(path, sb.ToString(), System.Text.Encoding.UTF8);
