# Requirements decisions

From the claims recovery workbench build. Source: `docs/decisions.md` in the build, cleaned for publication (no dates, account names, costs or outside links). The AI asked; the requester answered each in a line.

## Requirements clarifications (Erum)

| # | Question | Decision | Reason given |
| --- | --- | --- | --- |
| 1 | What counts as underpaid | Any shortfall against the contract rate | Not stated |
| 2 | Dollars at risk for a denial | Full contract-expected amount | Not stated |
| 3 | Contract terms | Fee schedule per procedure code only | Not stated |
| 4 | Payer rules that support a denial | Label "likely correct" | Not stated |
| 5 | Grain | Line level, rolled up to claim | Not stated |
| 6 | Ranking inputs beyond dollars | Appeal deadline | Not stated |
| 7 | Record staff decisions | No. Read-only | Not stated |
| 8 | Interface | AI/BI dashboard | Not stated |
| 9 | Appeal drafting | Out of scope | Not stated |
| 10 | Synthetic data size | Reasonable size | Not stated |
| 11 | Time or cost limit | None. Design to achieve the goal | Not stated |

## Acceptance-criteria open points (Erum)

| # | Question | Decision | Reason given |
| --- | --- | --- | --- |
| A | How the appeal deadline affects ranking | Dollars at risk, largest first; ties broken by earliest deadline. Past-deadline claims sort to the bottom, labeled "deadline passed" | Not stated |
| B | Where "likely correct" denials rank | Stay in the worklist, below the other denials | Not stated |
| C | Underpayment arithmetic | Patient's share is accounted for: shortfall = expected minus (payer paid plus patient responsibility) | Not stated |
| D | Procedure codes | Synthetic codes, not CPT | Not stated |

## Test-case open points (Erum)

| # | Question | Decision | Reason given |
| --- | --- | --- | --- |
| E | Claim with a likely-correct line and an actionable line | Claim stays actionable and ranks on actionable dollars only; likely-correct dollars in a separate column. Claim deadline = earliest actionable line deadline | Accepted recommendation |
| F | Older record arriving late | Latest remittance date wins, not arrival order | Accepted recommendation |
| G | Overpayments | Out of scope. Paid at or above contract is PAID_CORRECTLY and not flagged | Not stated |

## Test cases rev 3 (Erum)

| Decision | Reason given |
| --- | --- |
| Convert the locked test cases to machine-readable `tests/test_cases.json`, checked against the markdown by `tests/check_test_cases_sync.py` | So the checker reads fixed expected results and code cannot grade its own work |
| Duplicate payment hiding an underpayment, and a mid-year rate change, confirmed as already covered by TC-18 and TC-12/TC-13 | Erum asked to confirm coverage |

## Decision H (Erum)

| # | Question | Decision | Reason given |
| --- | --- | --- | --- |
| H | Valid claim line whose only payment record is held as invalid (TC-25, TC-27) | Line goes to NEEDS_REVIEW with reason "payment record held". AC-06 extended to match | Accepted recommendation |
