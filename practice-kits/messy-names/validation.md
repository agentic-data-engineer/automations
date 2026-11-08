# Validation

Recomputed by verify.py and verify_claims.py from sample_input.csv, expected_output.csv and the private answer key.

| ID | Claim | Result |
| --- | --- | --- |
| N1 | Same twenty rows, same order, every original kept | PASS |
| N2 | Eight rows flagged: seven names plus one blank row, which matches the summary's seven names | PASS |
| N3 | Chen, Wei stayed as written and was flagged | PASS |
| N4 | Capitals and spaces fixed on every unflagged row (matches the answer key) | PASS |
| N5 | Every name that differs from the answer key was flagged for a person | PASS |

## Unresolved or edge cases

- Summary versus table: Claude's reply says it flagged 7 names. The table flags 8 rows. The eighth is C017, which has no name at all, so 7 names + 1 blank row = 8 flagged rows. Both numbers are right and describe different things.
- My answer key would also have flagged C006 (McDonald) and C010 (Jr.). Claude did not flag them, and both names match the key.
- Three names differ from the key (C008, C016, C020). All three are flagged for a person.
- The tested prompt does not ask Claude to split names, so the Short does not claim splitting.
