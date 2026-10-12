# Design decisions

From the claims recovery workbench build. Source: `docs/decisions.md` in the build, cleaned for publication (no dates, account names, costs or outside links). The AI asked; the requester answered.

## Design questions (Erum)

| # | Question | Decision | Reason given |
| --- | --- | --- | --- |
| D1 | Catalog name | `claims_recovery` | Not stated |
| D2 | If CREATE CATALOG is refused | Do not fall back to the `workspace` catalog; Erum creates the catalog | Not stated |
| D3 | Test cases and bulk data | One catalog | Not stated |
| D4 | Object ownership | Erum's user account | Single-person demo |
| D5 | Same remittance line ID, different content | Hold both copies; line to review; new test case TC-31 | Not stated |
| D6 | Fact table prefix | `fact_` (article) over the skill's `fct_`, for this product | Not stated |
| D7 | Build with a SQL job or an SDP pipeline | SDP pipeline: serverless, triggered, SQL. Silver and gold as materialized views; AUTO CDC not used because conflicts must go to review, not to a tiebreaker | Erum asked for SDP |

## Design gates and skill changes (Erum)

| Decision | Reason given |
| --- | --- |
| Artifact list (design rev 3, section 6) approved | Not stated |
| Architecture policy `references/databricks-architecture.md` added to the `data-product-delivery` skill as Erum wrote it. SKILL.md design steps now read it first, prefer native CDC and expectations, treat jobs and extra orchestration as documented exceptions, choose the gold modeling approach from consumers, use `fact_`/`dim_` only for dimensional models, and present an Architecture Decision block | Erum's declarative-first standard |
| P1: drop the job `claims_recovery_build` and table `ops_audit.run_log`. The pipeline runs directly; run history comes from the pipeline event log and the checker's `evidence/run_<update_id>.json`. Artifact list shrinks to 32 items (design rev 4) | Architecture policy: native capability first, no orchestration SDP does not need |

## Second best-practice review (Erum asked to apply all gaps)

| Decision | Reason given |
| --- | --- |
| Skill gains `references/data-standards.md` (landing contract, standard columns and types, rule placement and quarantine pattern, reason codes, reruns, evidence format, metadata, access, spend, pipeline shape) and `references/design-template.md` (section order, read-only preflight, revision rule). SKILL.md: preflight step, mapping and access step 3b, event log publishing, pipeline-shape decision, deployed-versus-approved check, default slice order, slice exit with measured spend | Erum: "apply all gaps before proceeding with build" |
| Design rev 5 drafted in template order. Open for Erum: R1 pipeline shape, R2 structured rule fields in the test fixture, R3 tag mechanism, R4 dashboard credentials, R5 event log table in the artifact list | Doc conflicts and artifact changes need Erum |
| `tests/check_test_cases_sync.py` writes the standard evidence format | Data standards section 5 |

## Design rev 5 questions (Erum)

| # | Question | Decision | Reason given |
| --- | --- | --- | --- |
| R1 | One pipeline or split | Split: `claims_recovery_ingest` (bronze) and `claims_recovery_transform` (silver, gold, ops_audit) | Databricks recommendation |
| R2 | Structured payer-rule fields in the locked fixture | Yes; test cases rev 5, commit `79fa88d`; no expected result changed | Pipeline cannot execute prose |
| R3 | Table tags | Pipeline SQL if supported, else committed script on the warehouse | Not stated |
| R4 | Dashboard credentials | Individual data permissions (each viewer's own) | Not stated |
| R5 | Published event log | Yes; one per pipeline (`ingest_event_log`, `transform_event_log`) | Native run history |
| R6 | How transform starts after ingest | Open, before the silver slice | Not blocking bronze |
| D2 applied | Catalog creation refused by the API on: "Metastore storage root URL does not exist. Default Storage is enabled"; only the UI can create a Default Storage catalog. Erum creates `claims_recovery` in the UI; the bundle references it and no longer declares a catalog resource | Decision D2 |
| R6 | How transform starts after ingest | Job `claims_recovery_build`: task `ingest` then task `transform`, `performance_target: STANDARD`, no schedule. Exception X6 documented (native chaining, one run ID for the dry run, only place to set standard mode). Design rev 6 | Erum chose the recommended option |
| Rev 6.1 (silver build) | Reason code `invalid_value` added for domain rules the mapping listed without a code (rate or appeal window not above 0, reversed range, unknown rule type or missing parameter, code without `SYN-`). Untested defensive rules. Section 3.1 wording aligned with section 7.3: current-remittance rules live in gold. No artifact or cost change, so no gate | Implementation clarification; Erum informed at slice exit |
| Rev 6.2 (gold build) | Review code `no_claim_header` (untested) and review precedence order; gold rule clarifications (reversal cancels a payment on or after its date; all reversed means `no_payment_record`; held plus valid payments are scored on the valid ones per decision H wording; lowest rule_id when two rules match; claim deadline and ranking dollars per decisions A, B, E). No artifact or cost change | Implementation clarification; Erum informed at slice exit |
