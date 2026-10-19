# Worked example: hospital claims (synthetic)

A small slice of the answer key from the build shown in Season 1 of The Agentic Data Engineer. Every
identifier is synthetic.

Contract rates: payer A pays `SYN-100` at 100.00 before a contract change and 110.00 from the change
date, and `SYN-200` at 250.00; payer B pays `SYN-200` at 240.00. "Expected" means contract rate times
units. A line is underpaid when the payer paid less than expected minus the patient's share.

## The request (in plain words)

After a hospital treats a patient, it bills the insurer. Sometimes the claim is denied, and sometimes
it is paid less than the contract says. Find denied and underpaid lines, show the evidence, and rank
them by dollars at risk. A person always makes the final decision. All data is made up.

## A few test cases, written before any code

| Case | Category | Input | Expected status | Dollars at risk | Arithmetic |
| --- | --- | --- | --- | --- | --- |
| Correct, threshold at zero | Correct record | Payer A, `SYN-200` x1, paid 200.00, patient 50.00 | Paid correctly | 0.00 | 250.00 - 50.00 - 200.00 = 0.00 |
| Just over the threshold | Thresholds | Payer B, `SYN-200` x1, paid 190.00, patient 49.99 | Underpaid | 0.01 | 240.00 - 49.99 - 190.00 = 0.01 |
| Just under the threshold | Thresholds | Payer B, `SYN-200` x1, paid 190.01, patient 50.00 | Paid correctly | 0.00 | 240.00 - 50.00 - 190.01 = -0.01, so not flagged |
| Rate change, day before | Reference data over time | Payer A, `SYN-100` x1, service the day before the change, paid 90.00 | Underpaid | 10.00 | old rate 100.00 - 90.00 |
| Rate change, first day | Reference data over time | Payer A, `SYN-100` x1, service on the change date, paid 90.00 | Underpaid | 20.00 | new rate 110.00 - 90.00 |
| Short by exactly one dollar | Arithmetic trap | Payer A, `SYN-100` x1 (new rate), paid 80.01, patient 28.99 | Underpaid | 1.00 | 110.00 - 80.01 - 28.99 = 1.00 exactly; in floating-point math the same sum gives 0.9999999999999964 |
| Harmless duplicate | Duplicates | Payer A, `SYN-200` x1 sent twice identically, paid 200.00, patient 50.00 | Paid correctly | 0.00 | counted once, not twice |
| Same payment ID, different amounts | Duplicates | Payer A, `SYN-200` x1; one payment ID sent twice, once paid 200.00 and once 150.00 | Held for review | none | conflicting copies are never scored silently |

## The mistake the AI caught in its own draft

Its first acceptance criterion for synthetic data allowed "public procedure codes". Real procedure
codes are licensed, which breaks "all data is made up". It caught this on review, the criterion was
reworded to require synthetic codes, and the requester confirmed it.
