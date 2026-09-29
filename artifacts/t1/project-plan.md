# T1 Project Plan

## Project Goal

Develop a clear and reproducible research workflow for comparing three familiar asset classes:

- `SPY` — US equities
- `TLT` — long-term US Treasury bonds
- `GLD` — gold

The project begins with this written plan in T1; later tutorials will reuse the same repository to design a bounded analysis task and organize a verifiable agent workflow.

## Available Data

`data/etf_snapshot.csv` contains one row per illustrative ETF with these fields (defined in `data/data_dictionary.md`):

| Column | Unit | Meaning |
|---|---|---|
| `ticker` | — | Short identifier for the illustrative ETF |
| `asset_class` | — | Broad type of asset represented by the ETF |
| `expected_return_pct` | Percent per year | Illustrative annual return assumption |
| `volatility_pct` | Percent per year | Illustrative annual variability assumption |
| `max_drawdown_pct` | Percent | Illustrative largest peak-to-trough loss (negative values are losses) |
| `expense_ratio_pct` | Percent per year | Illustrative annual fund fee |

The dataset is **synthetic teaching data**: all values are illustrative assumptions, not live or historical market observations.

## Expected Final Deliverable

The T1 deliverable is this file, `artifacts/t1/project-plan.md`. It records the project goal, available data, milestones, a data limitation, and the next action so students can review it and save it with Git. Planned later work, such as the T2 comparative analysis, is described in the milestones as future work and is not yet completed.

## Three Project Milestones

1. **Write the project plan (T1)** — Create this plan from the repository overview and data dictionary, then review and save it with Git.
2. **Run a bounded comparative analysis (T2, planned)** — Use the instructor-prepared daily price data to compute and verify metrics such as expense ratios and maximum drawdowns, and produce a report with a manual check.
3. **Collect and check own data (optional, planned)** — Follow the dictionary's source links to check an issuer's expense ratio or obtain historical prices, then compare the results with the frozen inputs.

## One Data Limitation

All numeric values are synthetic teaching assumptions; they are not current quotations, verified historical estimates, or forecasts. The dataset also omits correlations, taxes, transaction costs, liquidity, currency exposure, and investor-specific constraints, so it must not be used as investment advice or as the basis for a real investment decision.

## Next Action

Review this plan, then save it with Git during T1 (JiuwenSwarm must not commit or push the change). Afterward, proceed to T2 using the instructor-prepared data pack.
