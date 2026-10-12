# Design: claims recovery workbench

From the claims recovery workbench build. Source: `docs/design.md` in the build, cleaned for publication (no dates, account names, costs or outside links). Approved rev 6. Sections 1 (sources), 2 (workspace check) and 12 (cost) are left out.

## 0. Architecture decision

```
Workload:        Batch file ingestion of claims, remittances and reference data; scoring of
                 denied and underpaid claims; ranked worklist with evidence
Consumers:       Revenue recovery staff through an AI/BI dashboard (BI, repeatable analytics)
Pipeline Style:  SDP, two serverless triggered pipelines in SQL: ingest (bronze) and transform
                 (silver, gold, ops_audit), as Databricks recommends (R1)
Reason:          Standard medallion with dependencies SDP can manage; no ML, APIs or side effects
Layers:          landing volume -> bronze streaming tables -> silver MVs -> gold MVs; ops_audit MVs
Incremental:     Bronze incremental via Auto Loader; silver and gold full recompute (small data,
                 cross-record rules)
Data Quality:    Quarantine pattern: held_reason computed once in silver, expectation on it for
                 counts, ops_audit.held_record lists rows (section 6)
Gold Grain:      Claim line; claim (worklist); remittance line; one row per payer, code, rate range, rule
Gold Modeling:   Dimensional (star). The consumer is a BI dashboard slicing by payer, status, tier and
                 procedure; prefixes dim_ and fact_ follow from that choice
Unity Catalog:   claims_recovery.{bronze, silver, gold, ops_audit}; section 11
Exceptions:      X2 to X4 and X6 below (X1 removed by P1; X5 resolved by R1)
```

| # | Exception | Native option considered | Why it is not enough | Portion affected |
| --- | --- | --- | --- | --- |
| X1 | ~~Job appending `ops_audit.run_log`~~ **Removed (P1)** | Pipeline event log | Not an exception any more: event log published to `ops_audit` (section 3) plus checker evidence | None |
| X2 | `ops_audit.held_record` view alongside expectations | Expectations | Expectations count violations but do not list which record or why. AC-13 needs row-level reasons. Uses the Databricks quarantine pattern, so each rule is written once | 1 view |
| X3 | Current-state rules in silver window functions instead of AUTO CDC | AUTO CDC | CDC resolves ties with a tiebreaker; TC-20 and TC-31 require conflicts to go to review (section 3.2) | Silver `remittance_line`, `claim_line` |
| X4 | Local Python: test-case loader, seeded generator, acceptance checker | Not pipeline logic | Test harness. Writes landing files and reads results; never transforms lakehouse data | `tests/`, `src/generate_synthetic.py` |
| X5 | ~~One pipeline for ingestion and transformation~~ **Resolved (R1)** | Databricks recommends separate pipelines | Erum chose the split | None |
| X6 | Job `claims_recovery_build` chaining the two pipelines (R6) | SDP manages dependencies only inside one pipeline; running the two by hand; table-update triggers | Two pipelines need something to run transform after ingest. A job is the native way to chain pipelines, gives the dry run one end-to-end run ID, and is the only place standard (cheaper) performance mode can be set. No business logic in the job | 1 job, 2 pipeline tasks |

## 3. Data flow

```
tests/test_cases.json ─┐  (reshaped by loader; never reads `expected`)
seeded generator ──────┴─> /Volumes/claims_recovery/bronze/landing/<source_table>/<load_id>.jsonl

SDP pipeline claims_recovery_ingest (serverless, triggered, SQL, development mode)
   -> bronze     streaming tables    STREAM read_files(), explicit STRING schema, provenance columns
   -> ops_audit  ingest_event_log    published natively

SDP pipeline claims_recovery_transform (serverless, triggered, SQL, development mode)
   -> silver     materialized views  typed, deduped, current state; held_reason + expectation
   -> gold       materialized views  dims + facts, stamped as_of_date
   -> ops_audit  materialized views  held_record, reconciliation
   -> ops_audit  transform_event_log published natively

Started: databricks bundle run claims_recovery_build  (job: ingest task, then transform task; standard mode)
A transform full refresh never resets bronze; an ingest full refresh re-reads the retained files
AI/BI dashboard (read-only)  ·  local checker -> evidence/*.json
```

Each pipeline writes with fully qualified names. `as_of_date` is a configuration value of the
transform pipeline (`` for all test runs); the ingest pipeline has none.

### 3.1 Why each layer uses what it uses

| Layer | Object | Reason |
| --- | --- | --- |
| Bronze | Streaming table per source | Auto Loader remembers files already read, so a rerun adds nothing (AC-11, AC-15). Append-only keeps all history |
| Silver | Materialized view per entity, fed by a temporary `*_checked` view that computes `held_reason` (quarantine pattern) | Rules compare records with each other: duplicate IDs with different content (TC-31), corrected claims (TC-22). Small data, which the medallion page names as the case for batch reads. Which remittance is current for a line (latest by date, reversals, conflicts: TC-20, TC-21, TC-23) is decided in gold `fact_remittance_line`, per its grain (section 7.3) |
| Gold | Materialized view per table | Matches the medallion page; recomputes deterministically for the rerun test |
| ops_audit | `held_record`, `reconciliation` as materialized views; `ingest_event_log`, `transform_event_log` published by the pipelines | Run history is native (P1) |

### 3.2 Why not AUTO CDC

AUTO CDC keeps the latest record per key, ordered by `SEQUENCE BY`, and breaks ties with a
tiebreaker column. The approved test cases need the opposite for conflicts: TC-20 and TC-31 go to
review, not to a winner. One silver view applies every current-state rule together in SQL window
functions.

### 3.3 Performance mode

The job runs both pipelines with `performance_target: STANDARD`.
A pipeline run by hand during development uses the default mode; the evidence records which ran.

## 4. Landing contract

| Rule | Setting |
| --- | --- |
| Volume | `claims_recovery.bronze.landing` (managed) |
| Path | `/Volumes/claims_recovery/bronze/landing/<source_table>/<load_id>.jsonl` |
| Source tables (folders) | `claim`, `claim_line`, `remittance_line`, `payer`, `procedure`, `fee_schedule`, `payer_rule` |
| Load IDs | `load_001` (test cases, load 1), `load_002` (test cases, load 2: TC-23 late record), `load_003` onward (generated data) |
| Immutability | A file is never overwritten or edited. A resend is a new load |
| Retention | Files are kept; no `cleanSource`. A full refresh re-ingests them |
| Format | JSON Lines, one record per line, field names as in the source (section 5) |

## 5. Source-to-target mapping

Bronze keeps source field names as `STRING`, plus `_load_id`, `_source_file`, `_ingested_at`,
`_rescued_data`. Silver renames to standard names and types.

**claim**

| Source field | Silver `claim` column | Type | Rule (held reason) |
| --- | --- | --- | --- |
| claim_id | claim_id | STRING | Required (`missing_required_field`) |
| payer_id | payer_id | STRING | Required (`missing_required_field`); unknown payer is review, not held |
| submitted_date | submitted_date | DATE | Must parse (`type_error`) |
| prior_auth_number | prior_auth_number | STRING | Optional; blank or spaces become NULL (decision R8, TC-34) |
| frequency_code | frequency_code | STRING | Default `1`; `7` = replacement |
| replaces_claim_id | replaces_claim_id | STRING | Optional; drives `is_superseded` on the original |
| (derived) | is_superseded | BOOLEAN | True when a later claim replaces this one (TC-22) |

**claim_line**

| Source field | Silver `claim_line` column | Type | Rule (held reason) |
| --- | --- | --- | --- |
| claim_id | claim_id | STRING | Required |
| line_no | line_no | INT | Must parse (`type_error`) |
| procedure_code | procedure_code | STRING | Required; no rate is review, not held |
| units | units | INT | Must parse (`type_error`); must be > 0 (`invalid_units`, TC-24) |
| date_of_service | service_date | DATE | Must parse (`type_error`); not after `as_of_date` (`future_date_of_service`, TC-26) |

Exact duplicate claim rows (TC-19) collapse to one row; they are not held.

**remittance_line**

| Source field | Silver `remittance_line` column | Type | Rule (held reason) |
| --- | --- | --- | --- |
| remit_line_id | remit_line_id | STRING | Required. Same ID with different content: both copies held (`duplicate_id_conflict`, TC-31). Identical copies collapse (TC-18) |
| claim_id, line_no | claim_id, line_no | STRING, INT | Required |
| remit_date | remit_date | DATE | Must parse (`type_error`) |
| status | remit_status | STRING | One of `PAID`, `DENIED`, `REVERSAL` (`invalid_status`, untested) |
| paid_amount | paid_amount | DECIMAL(18,2) | Must parse (`type_error`, TC-27); negative only on `REVERSAL` (`negative_paid`, TC-25) |
| patient_responsibility | patient_responsibility_amount | DECIMAL(18,2) | Must parse (`type_error`) |
| denial_code | denial_code | STRING | Required when `DENIED` (`missing_required_field`, untested) |

**Reference data**

| Source | Source fields | Silver table and columns | Rules |
| --- | --- | --- | --- |
| payer | payer_id, appeal_window_days | `payer`: payer_id STRING, appeal_window_days INT | Window > 0 (`type_error` if not a number) |
| procedure | procedure_code, description | `procedure`: procedure_code STRING, procedure_description STRING | Codes carry the `SYN-` prefix (AC-18) |
| fee_schedule | payer_id, procedure_code, rate, effective_from, effective_to | `fee_schedule_rate`: payer_id, procedure_code, rate_amount DECIMAL(18,2), effective_from_date DATE, effective_to_date DATE (NULL = open) | Rate > 0; ranges for one payer and code must not overlap (`overlapping_rate_range`, untested) |
| payer_rule | rule_id, payer_id, supports_denial_code, rule_type, max_days_after_service, procedure_code, requires_prior_auth | `payer_rule`: same names, typed | **R2:** the test fixture holds each rule's condition as prose; the pipeline needs these structured fields |

`procedure` has no source in the test fixture; the loader writes one row per `SYN-` code used, with
a placeholder description. It cannot change any expected result.

## 6. Rules and reason codes

| Outcome | Where decided | Reason codes |
| --- | --- | --- |
| **Held** (never scored; listed in `ops_audit.held_record`) | Silver `held_reason` column; expectation `valid_record EXPECT (held_reason IS NULL)` records counts | `type_error`, `missing_required_field`, `invalid_units`, `future_date_of_service`, `negative_paid`, `invalid_status`, `duplicate_id_conflict`, `overlapping_rate_range`, `invalid_value` (rev 6.1: rate or appeal window not above 0, reversed range, unknown rule type or missing rule parameter, code without `SYN-`; untested) |
| **Needs review** (valid, not scorable; visible with reason) | Gold `fact_claim_line_recovery`, first match in this order | `no_claim_header` (rev 6.2, untested: line whose claim header is missing or held), `unknown_payer`, `no_contract_rate`, `no_rate_on_date_of_service`, `conflicting_payment_records`, `payment_record_held`, `no_payment_record` |
| **Flagged** | Gold | Status `DENIED`, `UNDERPAID`; `likely_correct` with `rule_id`; tier `ACTIONABLE`, `LIKELY_CORRECT`, `DEADLINE_PASSED` |
| Correct | Gold | Status `PAID_CORRECTLY` |

Codes marked "untested" in section 5 have no locked test case; they are defensive and reported by the
held view.

Gold rules (rev 6.2, clarifications with no artifact or cost change):

| Rule | Setting | Source |
| --- | --- | --- |
| Current remittance | A PAID record is REVERSED by a reversal dated on or after it; the latest remaining record by `remit_date` is CURRENT; different outcomes sharing the latest date are CONFLICTING | Decision F, TC-20, TC-21, TC-23 |
| Everything reversed, nothing after | `no_payment_record` | Untested |
| Held and valid payments on one line | Scored on the valid ones; `payment_record_held` only when no valid payment exists | Decision H wording ("only payment record") |
| Two rules support one denial | Lowest `rule_id` is reported | Untested |
| Claim deadline | Earliest actionable line deadline; if none, earliest likely-correct deadline | Decision E, TC-05, TC-07 |
| Ranking dollars | Likely-correct dollars in the LIKELY_CORRECT tier, otherwise dollars at risk | Decisions A, B |

## 7. Gold design

### 7.1 Consumers

Revenue recovery staff, through the AI/BI dashboard: a ranked worklist, per-claim evidence, totals by
status, tier and payer, and the review queue. Queries filter by payer, status, tier and procedure.

### 7.2 Business processes

| Process | Source | Role in gold |
| --- | --- | --- |
| Claim billing | Claims and lines | Line and claim facts |
| Payer adjudication | Remittance lines | Remittance fact; drives line status |
| Contract pricing | Fee schedule, effective-dated | Rate dimension |
| Payer policy | Payer rules, appeal windows | Rule and payer dimensions |
| Recovery assessment | Derived | Line and claim recovery facts: the worklist |

### 7.3 Grain

| Table | Grain |
| --- | --- |
| `dim_payer` | One row per payer |
| `dim_procedure` | One row per synthetic procedure code |
| `dim_contract_rate_scd2` | One row per payer, procedure code and effective range |
| `dim_payer_rule` | One row per payer rule |
| `fact_remittance_line` | One row per unique remittance line ID, with its role: `CURRENT`, `SUPERSEDED`, `REVERSED`, `REVERSAL`, `CONFLICTING` |
| `fact_claim_line_recovery` | One row per current valid claim line |
| `fact_claim_recovery` | One row per current claim with at least one valid line; the worklist filters `in_worklist` |

### 7.4 Entities and keys

Natural keys: `payer_id`, `procedure_code`, `rate_id` (`payer_id` + `procedure_code` +
`effective_from_date`), `rule_id`, `claim_id`, (`claim_id`, `line_no`), `remit_line_id`.

### 7.5 Measures

All money `DECIMAL(18,2)` (TC-30).

| Measure | Defined at | Aggregation |
| --- | --- | --- |
| `expected_amount` = rate x units | Line | Additive |
| `paid_amount`, `patient_responsibility_amount` | Line (current remittance) | Additive |
| `shortfall_amount` = expected - (paid + patient) | Line | Additive; negative means overpaid, not flagged |
| `dollars_at_risk` | Line: expected if DENIED, shortfall if UNDERPAID, 0 if correct, NULL if review | Additive; claim sums actionable lines (decision E) |
| `likely_correct_dollars` | Claim | Additive |
| `worklist_rank` | Claim | Not additive |

Each measure is defined once, at the line. No pre-aggregated tables.

### 7.6 Relationships

| From | To | Cardinality |
| --- | --- | --- |
| `dim_payer` | `fact_claim_recovery` | 1 : N |
| `fact_claim_recovery` | `fact_claim_line_recovery` | 1 : N |
| `dim_procedure` | `fact_claim_line_recovery` | 1 : N |
| `dim_contract_rate_scd2` | `fact_claim_line_recovery` | 1 : 0..N |
| `dim_payer_rule` | `fact_claim_line_recovery` | 1 : 0..N |
| `fact_claim_line_recovery` | `fact_remittance_line` | 1 : 0..N |

### 7.7 History

| Layer | History |
| --- | --- |
| Bronze | Every record from every load, append-only |
| Silver | Current state; superseded claims flagged, not deleted |
| Gold | Current state stamped `as_of_date` |
| `dim_contract_rate_scd2` | Effective ranges |
| Run history | `ops_audit.ingest_event_log`, `ops_audit.transform_event_log`, plus checker evidence |

### 7.8 Reuse, enterprise entities, no duplicated metrics

No gold entities exist to reuse. The four dimensions and `fact_remittance_line` are enterprise
entities. No dashboard-specific tables; the review queue is `fact_claim_line_recovery` where
`status = 'NEEDS_REVIEW'`. `dollars_at_risk` is stored once per line and summed per claim.

### 7.9 Modeling approach

Dimensional star: four dimensions around line and claim facts, plus a remittance fact as the evidence
trail. Chosen for BI slicing (7.1). No bridge: each remittance line belongs to one claim line.

## 8. Reruns and evidence

| Item | Setting |
| --- | --- |
| Rerun comparison (AC-15) | All silver, gold and `ops_audit` materialized views, compared by content hash of sorted rows |
| Columns excluded | `_ingested_at`, pipeline update IDs, event-log contents |
| Bronze rerun rule | A second update with no new files adds zero bronze rows |
| Evidence format | Skill `references/data-standards.md` section 5 |
| Bronze slice evidence | `evidence/bronze_reconcile.json` (records written per file = bronze rows per load), `evidence/bronze_rerun.json`, `evidence/deployed_vs_approved.json`, `evidence/spend_bronze.json` |
| Later evidence | `evidence/AC-NN.json`, `evidence/run_<update_id>.json` |

## 9. Metadata and access

| Item | Setting |
| --- | --- |
| Comments | Every table and view; every gold column. Written in the pipeline SQL |
| Tags | `product = claims_recovery`, `layer = bronze/silver/gold/ops_audit`, `data_classification = synthetic` on every table. Pipeline tag `product = claims_recovery`. Whether SDP SQL can set table tags is verified in the bronze slice; fallback is a committed `src/sql/apply_tags.sql` run on the warehouse (R3, approved) |
| Ownership | Erum's user (D4) |
| Grants | None to other principals |
| Dashboard credentials | Individual data permissions: each viewer queries with their own access (R4) |
| Bundle target | One target, `dev`, **without** `mode: development`: that mode renames schemas to `dev_<user>_<name>` (caught by `bundle validate`, see `docs/ai-mistakes.md`). Pipelines set `development: true` themselves |

## 10. Well-architected pillars

| Pillar | How the design addresses it |
| --- | --- |
| Operational excellence | Pipeline SQL and dashboard in a bundle; one deploy command; event log published to Unity Catalog; standard evidence files |
| Security, privacy and compliance | Synthetic data only (AC-18); no secrets in the repo; read-only product (AC-17); no sensitive values in tags or comments; no grants beyond the owner |
| Reliability | Auto Loader checkpoints; immutable, retained landing files make full refresh safe; SDP manages dependencies and retries |
| Performance efficiency | Small data: no partitioning or clustering; predictive optimization on managed tables; dashboard reads gold only |
| Cost optimization | Serverless triggered pipeline, never continuous; no schedules; existing 2X-Small warehouse; spend measured per slice against the ceiling |
| Data and AI governance | Unity Catalog managed objects; comments and tags; lineage; held rows listed with reasons |
| Interoperability and usability | Delta, plain SQL, natural keys readable in evidence; dashboard for staff |

## 11. Artifact list (GATE: approved rev 6)

| # | Type | Full name | Notes |
| --- | --- | --- | --- |
| 1 | Catalog | `claims_recovery` | Created by Erum in the UI with Default Storage: the API refused (metastore has no storage root; Default Storage is UI-only), so D2 applied. Referenced by the bundle as `${var.catalog}`, not managed by it |
| 2-5 | Schemas | `claims_recovery.bronze`, `.silver`, `.gold`, `.ops_audit` | |
| 6 | Volume | `claims_recovery.bronze.landing` | Managed; section 4 |
| 7-13 | Streaming tables | `claims_recovery.bronze.` `claim_raw`, `claim_line_raw`, `remittance_line_raw`, `payer_raw`, `procedure_raw`, `fee_schedule_raw`, `payer_rule_raw` | Section 5 |
| 14-20 | Materialized views | `claims_recovery.silver.` `claim`, `claim_line`, `remittance_line`, `payer`, `procedure`, `fee_schedule_rate`, `payer_rule` | Section 5 |
| 21-27 | Materialized views | `claims_recovery.gold.` `dim_payer`, `dim_procedure`, `dim_contract_rate_scd2`, `dim_payer_rule`, `fact_remittance_line`, `fact_claim_line_recovery`, `fact_claim_recovery` | Section 7 |
| 28-29 | Materialized views | `claims_recovery.ops_audit.` `held_record`, `reconciliation` | |
| 29a | Event log table | `claims_recovery.ops_audit.ingest_event_log` | Published by the ingest pipeline (R5, R1) |
| 29b | Event log table | `claims_recovery.ops_audit.transform_event_log` | Published by the transform pipeline (R5, R1) |
| 30a | SDP pipeline | `claims_recovery_ingest` | Serverless, triggered, SQL, development mode; bronze only; tag `product` |
| 30b | SDP pipeline | `claims_recovery_transform` | Serverless, triggered, SQL, development mode; silver, gold, ops_audit; configuration `as_of_date`; tag `product` |
| 30c | Job | `claims_recovery_build` | Task `ingest` runs `claims_recovery_ingest`; task `transform` depends on it and runs `claims_recovery_transform`. `performance_target: STANDARD`. No schedule, no trigger. Tag `product` (R6) |
| 31 | AI/BI dashboard | `Claims Recovery Workbench` | Tiles approved at the development gate |
| 32 | Bundle deployment folder | In Erum's workspace home | `databricks bundle deploy` |

Not created: no run-log table, no new warehouse, no Genie space, no model serving, no
schedules, no continuous pipeline.

**Repo files**

| Path | Purpose |
| --- | --- |
| `databricks.yml`; one file per resource in `resources/`, named `<name>.<type>.yml` (`claims_recovery.catalog.yml`, `bronze.schema.yml`, `landing.volume.yml`, `claims_recovery_ingest.pipeline.yml`, ...) as `bundle validate` recommends | Bundle: schemas, volume, pipeline, dashboard. Variables `catalog`, `as_of_date`, `warehouse_id` (lookup by name) |
| `src/pipeline/ingest/bronze_ingest.sql` | Seven bronze streaming tables (ingest pipeline) |
| `src/pipeline/transform/silver_claims.sql`, `silver_remittance.sql`, `silver_reference.sql` | Silver views |
| `src/pipeline/transform/gold_dims.sql`, `gold_facts.sql` | Gold views |
| `src/pipeline/transform/ops_audit_held.sql`, `ops_audit_reconcile.sql` | Audit views |
| `src/generate_synthetic.py` | Seeded generator, standard library only |
| `tests/load_test_cases.py` | Writes landing files from `test_cases.json` inputs; never reads `expected` |
| `tests/check_bronze.py`, `tests/check_deployed.py`, `tests/check_spend.py`, `tests/apply_tags.py`, `tests/run_acceptance.py`, `tests/scan_boundaries.py` | Checks writing standard evidence |
| `tests/dbsql.py` | Shared helper: SQL through the CLI statement API with call and query timeouts |
| `tests/expectations.py` | Adds the generator's planted expectations (known by construction) to each check's hand counts once `load_003` has landed |
| `tests/check_silver.py`, `tests/check_reconciliation.py`, `tests/check_dashboard.py` | Silver, AC-14 and AC-16 checks |
| `src/dashboard/build_dashboard.py` | Generates the dashboard file from the approved tiles |
| `src/sql/apply_tags.sql` | Table tags (R3); SDP SQL cannot set tags |

## 13. Decisions

| # | Question | Decision |
| --- | --- | --- |
| D1 | Catalog name | `claims_recovery` (Erum) |
| D2 | If CREATE CATALOG is refused | Erum creates it; never use `workspace` (Erum) |
| D3 | Test cases and bulk data | One catalog (Erum) |
| D4 | Ownership | Erum's user (Erum) |
| D5 | Same remittance ID, different content | Hold both; line to review; TC-31 (Erum) |
| D6 | Fact prefix | `fact_` (Erum) |
| D7 | SQL job or SDP | SDP, serverless, triggered; no AUTO CDC (Erum) |
| P1 | Job and `run_log` | Removed; native history (Erum) |
| R1 | One pipeline, or ingestion separate from transformation? | Split: `claims_recovery_ingest` and `claims_recovery_transform` (Erum) |
| R2 | Structured payer-rule fields in the test fixture | Yes; test cases rev 5, commit `79fa88d` (Erum) |
| R3 | Table tag mechanism | Pipeline SQL if supported, else a committed script (Erum) |
| R4 | Dashboard credential mode | Individual data permissions (Erum) |
| R5 | Published event log in the artifact list | Yes; one per pipeline after R1 (Erum) |
| R6 | How the transform pipeline starts after ingest | Job `claims_recovery_build` with two pipeline tasks, standard mode (Erum). Exception X6 |
