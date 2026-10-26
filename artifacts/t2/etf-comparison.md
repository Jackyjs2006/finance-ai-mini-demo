# T2 ETF Comparison: Fees and Maximum Drawdown

This T2 analysis carries the T1 SPY/TLT/GLD comparison forward, upgrading from the synthetic T1 snapshot to the instructor-prepared T2 ETF Data Pack with real daily prices.

## Source and Settings

- **Input paths:** `data/t2/data_dictionary.md`, `data/t2/daily_prices.csv` (7,536 rows: 2,512 per ETF) and `data/t2/fund_info.csv`
- **Preparation date:** 2026-09-25 (fee recheck/access date 2026-09-25; retained price responses recorded as downloaded 2026-09-20)
- **Fee disclosure dates:** SPY — fund-information as-of 2026-09-10; fee-specific effective date not stated (accessed 2026-09-25). TLT — current prospectus; specific date not stated in the fee panel (accessed 2026-09-25). GLD — not stated in the selected field (accessed 2026-09-25)
- **Common drawdown period:** 2016-09-01 through 2026-08-31, inclusive
- **Frequency/adjustment:** daily (US market sessions; weekends and full-day closures absent), using `adjusted_close` (provider split and dividend-adjustment basis); `close` was retained as a provider field but not used
- **Script:** `artifacts/t2/calculate_drawdown.py`; command executed: `python3 -B artifacts/t2/calculate_drawdown.py`
- **Input check:** all passed (three tickers, no unknown tickers, 2,512 rows per ETF, unique ticker/date pairs, strictly ascending dates, positive finite prices, first/last dates 2016-09-01/2026-08-31, identical date sets across tickers)
- **Calculation method:** for each ETF in ascending-date order, cumulative maximum of `adjusted_close` from window start; drawdown = `(adjusted_close / cumulative_high - 1) * 100` at every date; maximum drawdown = most negative value, with the dictionary's earliest-trough/earliest-peak tie rule. Full precision kept internally; rounding applied only for display, per the dictionary. Fees come from issuer product pages (gross-of-waiver label for SPY; total expense ratio for TLT; GLD gross/net not stated).

## 1. Comparison

| Ticker | Annual expense ratio (%) | Max drawdown (%) | Peak date | Trough date |
|---|---|---|---|---|
| SPY | 0.0945 | -33.72 | 2020-02-19 | 2020-03-23 |
| TLT | 0.15 | -48.35 | 2020-08-04 | 2023-10-19 |
| GLD | 0.4 | -26.40 | 2026-01-29 | 2026-07-16 |

## 2. Observation

Among the three displayed two-decimal results, **GLD has the smallest drawdown loss** (-26.40%, closest to zero), followed by SPY (-33.72%) and TLT (-48.35%); there are no ties at two decimals. Drawdowns were **calculated from `adjusted_close`** as described above, not read from a precomputed snapshot, and all values are non-positive. These fees are issuer-disclosed snapshots accessed on 2026-09-25, not ten-year averages, and these drawdowns cover only the fixed 2016-09-01–2026-08-31 window — they are not all-time risk measures. One limitation: daily-close drawdown within this window does not capture intraday losses, transaction costs, or future performance.

## 3. Agent Check

The chosen ETF for the two-point recheck is **GLD** (the observation winner). This is my own self-check performed after drafting the report; it is not independent verification and no student checked the data.

**Source rows re-read from `data/t2/daily_prices.csv`** (recorded full precision):

- 2026-01-29, GLD, `adjusted_close` = `495.8999938964844` (reported peak)
- 2026-07-16, GLD, `adjusted_close` = `364.9599914550781` (reported trough)

**Check commands (separate source lookup + arithmetic):**

- Source lookup (kept from the original check): `awk -F, '($1=="2026-01-29"||$1=="2026-07-16") && $2=="GLD" {print}' data/t2/daily_prices.csv`
- Arithmetic (executable, no ellipsis): `python3 -B -c 'print((364.9599914550781/495.8999938964844-1)*100)'`

**Actual output:**

- Source lookup returned: `2026-01-29,GLD,495.8999938964844,495.8999938964844` and `2026-07-16,GLD,364.9599914550781,364.9599914550781` (row 7,390 before row 7,505 → peak on or before trough).
- Arithmetic command output: `-26.404517857029663`; displayed **-26.40%**.

**Comparison:** matches the report's GLD value of -26.40% exactly (float-exact equality with the full-series result); no correction needed.

**Method review:** the script iterated all 2,512 GLD dates computing the running cumulative high from window start and `(adjusted_close / high - 1) * 100` at every date; the most negative value occurs at the trough with its cumulative-high peak on or before it, which the two-point recheck above confirms for the reported pair. The two-point check alone does not prove this pair is the full-window minimum; the full-series method review (all dates, cumulative highs, peak-before-trough order) provides that separate check. Observation cross-check against all three results: GLD -26.40 vs SPY -33.72 vs TLT -48.35, so GLD is closest to zero with no ties — consistent with Section 2.

**Source limitation (per dictionary):** independent live confirmation of the retained historical data is still pending; internal consistency and calculation agreement do not establish source authenticity.