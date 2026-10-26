# T3 portfolio experiment — run notes

## Agent check summary (Independent Reviewer)

**Method.** The Reviewer wrote a separate Python 3 stdlib implementation,
`artifacts/t3/portfolio/review/review_t3.py` (never calling the starter
`scripts/t3_portfolio.py`), that re-derives every metric, candidate rule and
fingerprint from the raw JSON files. Reproduction commands are in
`artifacts/t3/portfolio/review/README.md`. A `--design-only` flag and an internal
gate guarantee the script never opens `inputs/test.json` or computes test
metrics unless `evaluation.json` exists and `gates-record.json` records the
candidate freeze (gate2) as APPROVED. Since the corrected final run, every
report goes to a FRESH uniquely named directory (`results-<label>-YYYYMMDD-HHMMSS`);
the script now refuses to write into any report directory that already contains
files (preflight + exclusive `xb` writes), so evidence is never overwritten.
Final full run: `review/results-final-20261027-011523/` (40 PASS, 0 FAIL, 0 SKIP,
4 INFO).

**Preservation disclosure.** The original pre-freeze report under
`review/results/` was overwritten by a later post-freeze rerun; the genuine
pre-freeze run (2026-10-27T00:51:22, 14 passed / 0 failed / 2 skipped / 2 info)
now survives only as a truncated excerpt in
`artifacts/t3/portfolio/source-events-evidence.json` (event
`original_prefreeze_review_run`). The current `results/` content is a post-freeze
rerun, not the original; no newer `--design-only` run is labeled a "restored
original" — it is a design-only recheck performed after the freeze.

**Checks performed (40 checks, all PASS, 0 FAIL).**
- (a) Enumerated all 67 candidates independently (66 ten-point-grid triples +
  exact equal thirds, asserted unique). Re-implemented the selection rule:
  design return ≥ baseline within 1e-12 (eligibility), minimize nonnegative
  max-drawdown loss magnitude, ties → baseline if present → higher return →
  ascending (SPY, TLT, GLD) weights, fallback retains the baseline unless the
  loss is strictly smaller beyond 1e-12. Result reproduces `candidate.json`
  exactly: 67 candidates, 39 eligible, tie size 1, min design loss 0.178136,
  improved=True, selected weights SPY 0.40 / TLT 0.00 / GLD 0.60.
- (b) Independently recomputed all four return/drawdown pairs AND the full
  design candidate curve/metrics straight from `inputs/design.json` prices with
  the frozen weights, matching each artifact to ~1e-12:
  - Design baseline: return +0.124258, drawdown −0.228130
  - Design candidate: return +0.339369, drawdown −0.178136
  - Test baseline: return +0.371293, drawdown −0.116445
  - Test candidate: return +0.633011, drawdown −0.170092
- (c) Curve lengths (design 754, test 500 closes), dates ascending and matching
  the period objects (design 2021-09-01→2024-08-30, test 2024-09-03→2026-08-31),
  nav[0]=100, per-point drawdown re-derived from running peaks. Source scope:
  `source_sha256` matches across design/test/manifest, and both window slices
  of the CSV match the prepared `design.json`/`test.json` prices exactly.
- (d) Candidate SHA-256 fingerprint verified: on-disk `candidate.json` bytes hash
  to `03a0ef1ac1481e75488e7e4acee6f6e26741410d91e70b8d3ab02eb804cf2095`,
  equal to `evaluation.json`'s `candidate_sha256` (the file evaluated is the
  file frozen). Evaluation weights equal the frozen candidate weights.
- (d2) Freeze/evaluation ordering verified by SAVED TIMESTAMPS from
  `source-events-evidence.json` (Codex read-only extraction), not gate booleans
  alone: plan approval relay 00:47:19 → evaluation dependencies added
  (update_task) 00:48:57 → original pre-freeze review run 00:51:22 → freeze
  approval relay 00:56:27 → freeze-gate completion 00:57:29 → evaluation tool
  call 00:58:04, strictly increasing. Disclosure: the evaluation dependency
  edges were added via `update_task` AFTER the initial `create_task` call (which
  omitted edges) but BEFORE any test evaluation ran (saved 00:48:57 < 00:58:04).
  These are saved event receipt times, distinct from the student's original
  click time.
- (e) Page `docs/t3/portfolio/index.html` (after the author's corrections):
  - Key conclusion box placed above the charts (`.key`), weights SPY 40.00% /
    TLT 0.00% / GLD 60.00%, all four table rows, both period phrases and both
    SHA-256 lines verified against the sources.
  - Worked-example section checked CELL BY CELL against `inputs/design.json` and
    the frozen weights: TLT closes 124.81542205810547 / 88.09698486328125, SPY
    and GLD closes, per-ticker weights (0.40 / 0.00 / 0.60), price ratios,
    weighted contributions, and the displayed arithmetic
    100 × 1.3393694951641868 = 133.9369495164187 (recomputed and matched).
  - All four SVG polylines (test baseline nav, test candidate nav, test baseline
    drawdown, test candidate drawdown) verified at the data level: 500 points
    each, dashes ordered (baseline dashed first, candidate solid second),
    date-axis labels 2024-09-03→2026-08-31 and three y-ticks per chart match the
    test window, and every plotted coordinate reproduces the evaluation curves
    with y = 220 − 175·(v − low)/(high − low).

**Key finding.** In the design window the candidate improved both return
(+33.94% vs +12.43%) and drawdown (−17.81% vs −22.81%) relative to the
equal-weight baseline. In the test window the candidate again returned more
(+63.30% vs +37.13%) but its maximum drawdown loss was larger (−17.01% vs
−11.64%): the design-period drawdown advantage did not persist. This is a valid
outcome under the brief; the page's Team interpretation states it honestly and
explicitly does not claim that any single asset caused the ranking.

**Approved candidate version.** `candidate.json` with initial weights
SPY 0.40 / TLT 0.00 / GLD 0.60, SHA-256
`03a0ef1ac1481e75488e7e4acee6f6e26741410d91e70b8d3ab02eb804cf2095`
(version 1, kind "candidate", design-period metrics as above).

**Freeze/evaluation sequence.** Plan approval (gate1, APPROVED) → design-stage
work (baseline-design.json, candidate.json) → candidate freeze (gate2,
APPROVED in `gates-record.json`; approval relay saved at 00:56:27, gate
completion at 00:57:29) → evaluation.json produced only after the freeze
(tool call saved at 00:58:04), with `candidate_sha256` matching the frozen file
→ page → this review. Gate3 (final acceptance) remains PENDING. _(Leader supersession note added later: the student subsequently granted Gate 3 acceptance — see the Leader "My three decisions" section below; the Reviewer's original wording is retained unchanged, and this sentence no longer reflects the final record.)_

**Unresolved issue.** None in the calculations or handoffs. One process note:
the machine had no Chrome/Chromium binary (a Safari WebDriver attempt also timed
out), so a pixel-level automated browser render check of `index.html` could not
be executed here; HTTP/source checks are not a visual render check. The page
content, worked-example cells and chart SVGs were verified by source/structural
inspection and data-level re-derivation instead. A human opening the page in
Safari (or any browser) should confirm the visual rendering before gate3. The
Reviewer does not claim student acceptance (gate3 is the student's decision, not
verifiable from files).

## My three decisions (Leader section — appended by the Leader from the student's exact chat words)

The three decisions below are recorded from the student's exact chat words,
preserved verbatim (also stored machine-readable in `gates-record.json`). The
Independent Reviewer's Agent check summary above is preserved unchanged.

**1. Plan approval (Gate 1, APPROVED).** Exact words: "I approve the corrected
T3 plan for preparation, synthetic tests, and design-stage work only. The plan
clearly assigns responsibilities to different agents, includes an independent
Reviewer, and ensures that test evaluation cannot begin before I approve and
freeze the portfolio weights. This separation is important to prevent
test-period information from influencing portfolio selection and to ensure a
fair evaluation. Please proceed with preparation and design only. I will review
and approve the candidate portfolio separately before testing, and make my
final acceptance decision after reviewing the completed results."

**2. Waiting prediction (student's own words).** Exact words: "I expect the
test evaluation task to wait until the agents have selected a portfolio using
the design-period data and I have approved the final weights. This is because
the portfolio weights must be fixed before testing, so the test-period results
cannot influence the selection. This ensures a fair comparison with the
benchmark."

**3. Candidate freeze (Gate 2, APPROVED) and visual verification.** Exact
approval words: "i approve all". Exact freezing reason: "I approved freezing
the 40% SPY, 0% TLT, and 60% GLD portfolio because the weights were selected
using design-period data, and keeping them unchanged during testing prevents
look-ahead bias and ensures a fair comparison with the benchmark." Exact visual
check confirmation (genuine Safari check of the corrected page; received via
Codex relay, no click-time envelope available, `recorded_at: null`): "Yes,
everything displays clearly". The visual check is verification only — not Gate
3 final acceptance and not Git/publishing permission.

**Prediction versus actual dependency sequence.** The prediction held: the
candidate was selected using design-period data only; the student approved and
froze the weights (Gate 2, relay received 00:56:27, freeze-gate completion
00:57:29, both Asia/Hong_Kong saved times) before any test evaluation; the
evaluation tool call (00:58:04) ran only after the freeze and
`evaluation.json`'s `candidate_sha256` matches the frozen file. One setup
correction is disclosed: the evaluation dependency edges were added via
`update_task` AFTER the initial `create_task` call, at 00:48:57 — they were in
place before the first test evaluation (00:58:04). One report replacement is
disclosed: the original pre-freeze review report under `review/results/` was
overwritten by a later post-freeze rerun; it survives only as a truncated
excerpt in `source-events-evidence.json`, and no newer report is labeled as the
original (see the Reviewer's preservation disclosure above).

**Gate 3 (final acceptance).** STATUS: ACCEPTED (supersedes the earlier
PENDING statements above — including "The Leader stops here for the student's
decision" and the Reviewer's original "remains PENDING" line, which are
retained in this document and marked superseded; no earlier acceptance is
implied). Exact acceptance words: "I accept the final T3 result because the
independent review reported 40 passed checks with no failures, and the results
clearly show that the frozen portfolio achieved a higher return but a worse
maximum drawdown than the benchmark. I understand that the portfolio did not
achieve the intended drawdown improvement during testing, but the findings and
limitations are transparently reported. I also acknowledge the disclosed
review-report replacement and retained trace." The exact student click time is
unavailable and no click timestamp is invented; any saved runtime receipt is a
relay receipt only. Acceptance covers the results and their transparent
reporting. No Git actions or publishing are authorized: Codex will separately
review and stage only the two required deliverables, then request commit/push
approval. The frozen candidate, evaluation, page, review reports, original data
and all prior decisions remain unchanged.