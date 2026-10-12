# Answer key: two of the locked test cases

From the claims recovery workbench build. Source: `docs/test-cases.md` in the build, cleaned for publication (no dates, account names, costs or outside links). Expected results were written before any code and locked in git. Two of the cases shown in the video:

- **TC-18**: a duplicate payment that would hide an underpayment (covered before the requester asked)
- **TC-30**: a claim short by exactly one dollar (added from the requester's review)

<!-- synthetic test data -->

| ID | Category | Input | Expected status | $ at risk | Other expected | AC |
| --- | --- | --- | --- | --- | --- | --- |
| TC-18 | Duplicate that would hide a problem | PYR-B SYN-100 x1, DOS 2026-08-01, paid 70.00, Pt 20.00, paid 2026-08-01; same remittance line ID sent twice | UNDERPAID | 5.00 | Counted once. Summing both would show 140.00 paid and hide the shortfall. Deadline 2026-09-30 | 11 |
| TC-30 | Short by exactly one dollar | PYR-A SYN-100 x1, DOS 2026-08-01, paid 80.01, Pt 28.99, paid 2026-08-20 | UNDERPAID | 1.00 | Deadline 2026-11-18. Float math gives 0.9999999999999964; exact decimal gives 1.00 | 02 |

<!-- /synthetic test data -->

All identifiers, amounts and dates are synthetic.
