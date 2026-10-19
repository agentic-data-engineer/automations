# Worked example: a claims worklist on Databricks

The build behind the videos, in one page. A learning build on synthetic data.

## What the customer wanted

A fictional hospital's revenue recovery team needs to decide which denied or underpaid claim to work
next. The evidence is scattered across claims, payment records, contract rates and payer rules. They
want one prioritized worklist: how much money is at stake, what the evidence shows, and which claims need
attention first. Staff make the final decision. The tool flags and shows evidence; it never submits a
claim, files an appeal, collects money or posts a payment.

## What review changed

| Phase | The AI drafted | What review caught | What changed |
| --- | --- | --- | --- |
| Requirements | 18 acceptance criteria, 29 test cases | The data engineer's questions (a duplicate payment hiding an underpayment, a rate changing mid-year) were already covered | Answer key locked in git before any code |
| Design | A medallion design with the pipeline choice left open | One question: "are you creating SDP pipelines?" | Redesigned around Spark Declarative Pipelines; ingestion and transformation split |
| Build | Loader, clean layer, worklist | The API couldn't create the catalog; the rule was to stop, not switch | The AI stopped; the data engineer created the catalog |
| Build | An acceptance check | One requirement had no test cases mapped to it; the check refused to pass | Coverage added (and later expanded after review) |
| Testing | Tests that all passed | 12 planted bugs; 2 got through | Two new test cases; all 12 caught |
| Review | A finished build | A second AI returned 5 findings, incl. a blank authorization number treated as present | Each fix proved by a failing test first; 4 more planted bugs, all 16 caught |
| Sign-off | | Clean rebuild, rerun matching across 23 tables | All 18 criteria signed off by the data engineer |

## What it was not

A learning build. Production would still need code and security reviews, and testing by the revenue
recovery staff on their own workflows.

---
From The Agentic Data Engineer starter kit. CC BY 4.0.
