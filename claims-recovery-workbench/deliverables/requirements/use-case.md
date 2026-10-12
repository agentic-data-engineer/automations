# Use case

From the claims recovery workbench build. Source: `docs/use-case.md` in the build, cleaned for publication (no dates, account names, costs or outside links). The request, restated by the AI and confirmed.

## Business problem

After a hospital treats a patient, it bills the insurer (payer). Some claims are denied. Some are
paid less than the contract says. The proof sits in four separate places: the claim, the payment
record, the contract rates and the payer's rules. Staff cannot join them by hand at volume, so money
the hospital is owed goes uncollected.

## Users

Revenue recovery staff who work denied and underpaid claims. The product is read-only for them.

## Decisions the product supports

| Decision | What the product gives the person |
| --- | --- |
| Which claim to work next | A worklist ranked by dollars at risk, largest first |
| Is this claim really denied or underpaid | The evidence from all four sources, side by side |
| How much is at stake | Expected amount, paid amount, and the gap, with how each was derived |

A person always makes the final decision.

## What it must never do

- Submit or resubmit claims.
- File appeals.
- Collect money or post payments.
- Make medical decisions (medical necessity, coding correctness).
- Act on its own findings. It flags and explains; staff decide.
- Flag overpayments (out of scope, decision G).
- Draft appeal text (out of scope, decision 9).

## Sources

| Source | Holds |
| --- | --- |
| Claims | What was billed: claim, lines, services, charges, dates, payer |
| Payment records | What the payer paid or denied, with reason codes |
| Contract rates | What the payer agreed to pay, by service, effective-dated |
| Payer rules | Payer policies that explain or invalidate a denial or reduction |

## Data rules

- Synthetic data only. No real patients, payers, providers or money.
- Deterministic code does the scoring; every result carries its evidence.

## Scoring scope (Erum)

| Topic | Decision |
| --- | --- |
| Underpaid | Any shortfall against the contract rate. No threshold |
| Dollars at risk, denial | The full contract-expected amount |
| Contract terms | Fee schedule per procedure code only |
| Payer rules | A rule that supports the denial labels it "likely correct". It is not removed |
| Grain | Score each service line, roll up to the claim |
| Ranking | Dollars at risk, with the appeal deadline factored in |
| Write-back | None. Read-only |
| Interface | Databricks AI/BI dashboard |
| Appeal drafting | Out of scope |
| Data size | Reasonable synthetic volume |
| Time and cost limit | None. Design to achieve the goal |
