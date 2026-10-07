# Glossary

Plain-language terms used in the series. US healthcare billing terms; the build uses made-up data only.

## Hospital billing

| Term | Meaning |
| --- | --- |
| Claim | The bill a hospital or clinic sends to an insurer for care it provided |
| Claim line | One service on a claim: a procedure code, units and a charge |
| Payer | The insurer or health plan that pays the claim |
| Payment record (remittance) | The insurer's statement of what it paid, adjusted or denied, line by line. In the US this is usually an electronic remittance advice (the 835 transaction) |
| Denial | The insurer refuses to pay a line or claim, with a reason code |
| Underpayment | The insurer paid, but less than the contract says it should |
| Contract rate, fee schedule | The amount the hospital and insurer agreed for each procedure code |
| Expected amount | What the contract says should be paid for a line |
| Patient responsibility | The part the patient owes: deductible, copay or coinsurance |
| Prior authorization | Approval the insurer requires before certain care |
| Timely filing limit | The deadline for submitting a claim |
| Appeal, appeal deadline | A formal request to reverse a denial or underpayment, and the date by which it must be filed |
| Revenue cycle | Everything from registering a patient to collecting payment |

## Terms from this build

| Term | Meaning |
| --- | --- |
| Dollars at risk | For a denied line, the expected amount; for an underpaid line, the shortfall |
| Worklist | Flagged claims ranked by dollars at risk, largest first; past-deadline claims at the bottom |
| Likely-correct denial | A denial that matches one of the insurer's rules, so it probably should not be appealed. Shown, ranked lower, never hidden |
| Held record | Invalid input (negative or impossible values) kept aside with a reason, never scored |

## Data and delivery

| Term | Meaning |
| --- | --- |
| Bronze, silver, gold | The medallion layers: raw data as it landed, cleaned and joined data, and business-ready tables |
| Pipeline | Code that moves data through the layers; here, Spark Declarative Pipelines on Databricks |
| Unity Catalog | Databricks' layer for organizing data and controlling who can see it |
| AI/BI dashboard | Databricks' built-in dashboard |
| Acceptance criteria | The statements that must be true for the product to be accepted (18 here) |
| Test case | One input with the result expected, written before the code (34 here) |
| Mutation testing | Deliberately planting bugs to prove the tests catch them (16 planted, 16 caught) |
| Dry run | A full end-to-end run on a clean environment before anyone relies on it |
| UAT | User acceptance testing by real business users; outside this build's scope |
