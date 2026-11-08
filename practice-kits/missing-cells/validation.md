# Validation

Recomputed 2026-11-08 by verify.py and verify_claims.py from sample_input.csv, expected_output.csv and the private answer key.

| ID | Claim | Result |
| --- | --- | --- |
| F1 | Six product rows filled, every fill labelled | PASS |
| F2 | Gift card left blank | PASS |
| F3 | All eighteen rows match the answer key | PASS |
| F4 | Subtotals plus the gift card equal the grand total | PASS |
| F5 | Nine intentional blanks protected | PASS |
| F6 | A fill-down would put Software on the Gift card row | PASS |

## Unresolved or edge cases

- 18 of 18 means all 18 rows of this practice file matched the answer key: 6 filled, 9 left blank on purpose, 3 categories already present. It says nothing about other files.
- The tested prompt never names the gift card. It gives a general rule, and Claude applied it on this file. This does not show it knows every business rule.
- The orange "Software?" cell is an illustration of what a fill-down would guess, labelled on screen. It is not AI output.
- The tested prompt asks for a note on every fill and to leave other blanks alone. It does not use the word flag.
