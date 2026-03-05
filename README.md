# TXN-v4-complete

Debit card transaction analysis notebook for credit union conference presentations. 301 modular cells across 23 analysis sections, designed for a 5,000-person room.

## Architecture

Each section is a numbered folder containing standalone Python cells. Cells are loaded into a Jupyter notebook in order. Every cell includes data guards (`if 'var' not in dir()`) so sections can run independently.

**Data sources:** ODDD transaction extract (`combined_df`), rewards/demographics file (`rewards_df`), optional campaign mail/response columns, optional balance snapshots.

## Sections

| # | Folder | Cells | Description |
|---|--------|-------|-------------|
| 00 | `00-setup` | 10 | Data loading, file paths, library imports |
| 01 | `01-general` | 29 | Portfolio theme, color palette, KPIs, engagement tiers, age/spend analysis |
| 02 | `02-merchant` | 14 | Top merchant aggregation, spend concentration, merchant trends |
| 03 | `03-mcc-code` | 15 | MCC category analysis, category spend by demographics |
| 04 | `04-business-accts` | 14 | Business account merchant analysis, spend patterns |
| 05 | `05-personal-accts` | 14 | Personal account merchant analysis, spend patterns |
| 06 | `06-direct-competition` | 35 | Competitor detection, wallet share, threat assessment |
| 07 | `07-financial-services` | 19 | Financial services transaction patterns, provider analysis |
| 08 | `08-ics-acquisition` | 10 | ICS acquisition source analysis, channel performance |
| 09 | `09-ars-campaign` | 25 | ARS mail campaign: cohort lift, DID analysis, responder vs non-responder |
| 10 | `10-branch` | 10 | Branch-level transaction analysis, top branch ranking |
| 11 | `11-transaction-type` | 8 | PIN vs signature, transaction type distribution |
| 12 | `12-product` | 10 | Product code analysis, product mix |
| 13 | `13-attrition` | 12 | Attrition risk tiers, spend velocity, early warning signals |
| 14 | `14-balance` | 10 | Balance bands, balance trends, balance vs spend correlation |
| 15 | `15-interchange` | 10 | PIN/signature interchange, revenue waterfall, opportunity gap |
| 16 | `16-rege-overdraft` | 10 | Reg E opt-in trends, overdraft limit distribution, revenue exposure |
| 17 | `17-payroll` | 10 | Payroll/direct deposit detection, PFI composite scoring |
| 18 | `18-relationship` | 10 | Product cross-holdings, relationship depth, next-best-product |
| 19 | `19-segment-evolution` | 8 | Bimonthly segmentation migration, campaign segment impact |
| 20 | `20-retention` | 7 | Account health classification, churn by segment, dormancy funnel |
| 21 | `21-engagement-migration` | 6 | Monthly engagement tier migration, net flow, migration matrix |
| 22 | `22-executive` | 5 | Executive scorecard dashboard, strategic priorities, action roadmap |

## Cell Naming Convention

```
<section>/<sequence>_<description>
```

- `01_*_data` -- data pipeline (runs first, produces DataFrames)
- `02_*_kpi` -- KPI summary cards (FancyBboxPatch)
- `03`-`09` -- visualizations (bar charts, heatmaps, scatter plots, waterfalls)
- `10_action_summary` -- findings table + recommended actions

## Design Patterns

- **Separate figures**: Each chart is its own `fig, ax = plt.subplots()`. No multi-panel layouts.
- **Conference styling**: Large fonts (title 26pt, labels 16pt), bold text, `GEN_COLORS` palette.
- **KPI cards**: `FancyBboxPatch` with 3-line layout (label, value, subtitle).
- **Data guards**: Every cell starts with `if 'var' not in dir()` checks. No `try/except NameError`.
- **Action summaries**: Styled HTML table with priority-colored rows (High/Medium/Low).
- **Campaign responder logic**: TH 10/15/20/25 + NU 5+ = Responder; NU 1-4 or null = Non-Responder.

## Color Palette

Defined in `01-general/01_general_theme` as `GEN_COLORS`:

| Key | Hex | Use |
|-----|-----|-----|
| `primary` | `#1B4332` | Dark green, headers |
| `secondary` | `#2D6A4F` | Medium green, secondary elements |
| `accent` | `#E63946` | Red, alerts and negative values |
| `success` | `#2D6A4F` | Green, positive values |
| `warning` | `#E9C46A` | Amber, caution indicators |
| `info` | `#457B9D` | Blue, informational |
| `dark_text` | `#1D3557` | Navy, titles and labels |
| `muted` | `#6C757D` | Gray, subtitles |
| `grid` | `#E9ECEF` | Light gray, gridlines |

## Requirements

```
pandas
numpy
matplotlib
seaborn
```

## Usage

1. Place ODDD and rewards CSV files in the data directory
2. Run `00-setup` cells to load data
3. Run `01-general` to establish theme and engagement tiers
4. Run any subsequent section -- each is self-contained with data guards
5. `22-executive` should run last (aggregates KPIs from all prior sections)
