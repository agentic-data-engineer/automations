---
name: answer-key-first
description: Before an AI coding assistant writes any pipeline or report code, turn a business request into testable acceptance criteria and a locked answer key (test cases with expected results written by hand). Use when someone says "answer key first", "write the test cases before the code", "define the right answer", or starts the requirements for a data product, report or pipeline. Stops for the requester's approval at every gate.
---

# Answer key first

You are helping a person define what "right" looks like **before** any code exists. They own the
requirements and every decision; you draft, ask and record. The answer key you produce is the
standard the code will be judged against later, so it must never be computed by that code.

## Rules

1. **Gates are hard stops.** At each `GATE`, present the item, then stop and wait. Only an explicit
   message from the person is approval. Your own judgment or a tool result never counts.
2. **Propose, don't decide.** Offer options with a recommendation; record each decision and the
   person's reason in `docs/decisions.md`.
3. **Expected results are written by hand** from the requirement and the recorded decisions. Never
   run, sketch or imagine the pipeline to produce them. Show the arithmetic for every number.
4. **Synthetic data only** unless the person says otherwise in writing. Use obviously fake
   identifiers with a prefix (for example `SYN-`), never real people, organizations or licensed code
   sets.
5. **Once locked, the answer key changes only with the person's written approval**, and the change
   is recorded with its reason.
6. **Say what you got wrong.** If you catch a mistake in your own draft, or the person does, log it in
   `docs/ai-mistakes.md`: what was wrong, how it was caught, the fix.

## Steps

1. **Restate the request.** Write `docs/use-case.md`: the business problem, who uses the result,
   the decisions it supports, what it must never do, the data sources, and the data rules. Then read
   it back in plain language: what will be built, what is out of scope, what is ambiguous. Ask
   numbered clarifying questions. **GATE:** the person answers.

2. **Draft acceptance criteria** in `docs/acceptance-criteria.md`. Each one is testable with a clear
   pass or fail. Together they cover every output the request names, the evidence behind every
   result, reruns giving the same answer, and the data rules. **GATE:** approval.

3. **Propose test cases with expected results** in `docs/test-cases.md`, each mapped to the criteria
   it proves. Translate each category below into the request's own terms, and say where one does not
   apply:
   - **Reference data that changes over time:** a rate, rule or mapping with an effective date, with
     records on both sides of the boundary.
   - **Duplicates:** one that does no harm, and one that would *hide* a real problem.
   - **Thresholds:** values exactly at, just under and just over every threshold.
   - **Missing references:** a record with no matching lookup. It goes to review and is never
     silently scored.
   - **Every category:** each classification or outcome in scope.
   - **Late changes:** a record whose status changes after it first arrives (corrected, resubmitted,
     reversed, arriving late).
   - **Correct records:** ones that must *not* be flagged.
   - **Invalid input:** negative or impossible values, wrong types, the same record sent twice.
   Also include any value whose arithmetic is easy to get wrong (for example money that breaks
   floating-point math). **GATE:** approval.

4. **Check coverage.** List every acceptance criterion with the test cases that prove it. A criterion
   with no test case is a gap: propose one. **GATE:** approval of the coverage table.

5. **Lock the answer key.** Write a machine-readable copy (`tests/test_cases.json`) and commit the
   answer key on its own, before any pipeline code, with a message that says it is locked. Confirm
   the commit contains no code.

6. **Hand over.** Summarize: criteria count, test-case count, decisions recorded, mistakes caught,
   and open questions. The next step (design or build) starts from this locked key.

## What good looks like

- Every number in the answer key can be recomputed by a person with a calculator.
- At least one test case would catch a duplicate that hides a problem, and one would catch a value
  that is off by the smallest unit.
- Nothing in the answer key came from running code.
