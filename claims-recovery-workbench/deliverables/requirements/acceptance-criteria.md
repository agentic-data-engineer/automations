# Acceptance criteria

From the claims recovery workbench build. Source: `docs/acceptance-criteria.md` in the build, cleaned for publication (no dates, account names, costs or outside links). Approved. Each one passes or fails.

`evidence/AC-NN.json` with `"result": "PASS"` or `"FAIL"`.

Terms: a **line** is one service line on a claim. **Expected** = contract fee-schedule rate for the
procedure code, effective on the date of service, times units. **Shortfall** = expected minus
(payer paid plus patient responsibility), in cents (decision C). **As-of date** = a fixed run
parameter; a deadline is passed when it is earlier than the as-of date.

## Scoring

| ID | Criterion | Pass when |
| --- | --- | --- |
| AC-01 | Denied lines are found | Every line whose current payment status is denied is flagged `DENIED`, dollars at risk = expected. No other line is `DENIED` |
| AC-02 | Underpaid lines are found | Every paid line with shortfall > 0.00 is flagged `UNDERPAID`, dollars at risk = shortfall. No other line is `UNDERPAID` |
| AC-03 | Correct lines are not flagged | Lines paid at or above expected are `PAID_CORRECTLY` and absent from the worklist. Overpayments are not flagged (decision G) |
| AC-04 | Right rate for the date | Expected uses the rate row whose effective range contains the date of service, including lines on each side of a rate change |
| AC-05 | Supported denials are labeled | A denial matching a payer rule that supports it carries `likely_correct = true` and names the rule. It stays visible, per decision 4 |
| AC-06 | Missing references go to review | A line with no contract rate, no payment record, an unknown payer, or whose only payment record was held as invalid (decision H) is `NEEDS_REVIEW` with a reason, and has no dollars at risk |
| AC-07 | Claim rollup ties out | Claim dollars at risk = sum of its actionable line dollars, to the cent. Likely-correct line dollars roll up to a separate claim column. Claim deadline = earliest deadline among its actionable lines (decision E) |

## Worklist

| ID | Criterion | Pass when |
| --- | --- | --- |
| AC-08 | Appeal deadline is shown | Each flagged claim shows its appeal deadline, derived from the payer's appeal window and the denial or payment date |
| AC-09 | Ranking follows the approved rule | Three tiers, in order: (1) actionable claims, (2) "likely correct" denials, (3) "deadline passed". Within each tier: dollars at risk descending, then earliest deadline, then claim ID. Same order on every run (decisions A, B) |
| AC-10 | Evidence behind every result | Each flagged line shows the source IDs and values from all four sources used (claim line, payment record, rate row, rule if any) and the arithmetic that produced its dollars |

## Data integrity

| ID | Criterion | Pass when |
| --- | --- | --- |
| AC-11 | No double counting | A duplicate source record never adds dollars or rows. A duplicate that would hide a problem (for example a second "paid" record masking a denial) is caught and sent to review |
| AC-12 | Late changes use the current state | Corrected, resubmitted, reversed or late-arriving records resolve to the latest valid state, ordered by remittance date, not arrival (decision F), with history kept in bronze |
| AC-13 | Invalid input is held, not scored | Negative or impossible values and wrong types are held in `ops_audit` with a reason and never scored |
| AC-14 | Layers reconcile | Row counts and dollar totals tie from bronze to silver to gold. Every dropped or held row is explained in `ops_audit` |
| AC-15 | Reruns are identical | A second full run produces identical gold output and row counts |

## Product boundaries

| ID | Criterion | Pass when |
| --- | --- | --- |
| AC-16 | Dashboard answers the use case | The AI/BI dashboard shows the ranked worklist, per-claim evidence, and totals by category and payer, and each figure ties to gold |
| AC-17 | Read-only, never acts | No component writes outside the product catalog or produces claim submissions, appeals or payment postings. Checked by a committed scan of the code and job definitions |
| AC-18 | Synthetic data only | All data is synthetic: bulk data comes from the committed, seeded generator, and test data from the committed, locked, hand-written fixture (`tests/test_cases.json`). Every bronze record traces to a file listed in one of those two manifests. Identifiers (patients, providers, payers, procedure codes) use a synthetic prefix. No real people, organizations or licensed code sets (reworded, decision R7, Codex F4) |
