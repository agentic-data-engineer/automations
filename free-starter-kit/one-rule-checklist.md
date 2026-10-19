# The one rule checklist

> **AI drafts. You decide. No phase is complete without your sign-off.**

The AI proposes; you approve, change or reject. Each phase ends at a gate, and only your explicit
"approved" opens it. Tick every box before you say it.

## 1. Requirements: define the boundaries

- [ ] I can say in one sentence what the product must **never** do (for example: never submits, never pays, never deletes).
- [ ] Every acceptance criterion has a clear pass or fail.
- [ ] The expected answers (the answer key) were written **by hand, before any code**, and committed on their own.
- [ ] The answer key includes the awkward cases: a duplicate that *hides* a problem, a value exactly at a threshold, a rate that changes mid-period, invalid input, records that must **not** be flagged.
- [ ] Every criterion maps to at least one test case.

**Gate:** I approve the criteria and the answer key. Only then does design begin.

## 2. Design: settle the choices

- [ ] Every open choice was put to me as options with a recommendation, not decided silently.
- [ ] I know which documented platform features the design relies on, and I've read the current docs for each.
- [ ] I've seen the full list of everything that will be created: catalogs, schemas, tables, pipelines, dashboards.
- [ ] There is a stated spend ceiling, and the AI stops and asks before passing it.
- [ ] I asked for at least one more review pass ("check this against best practices").

**Gate:** I approve the design and the artifact list. Only then does the build begin.

## 3. Build: one slice at a time

- [ ] Nothing was deployed that the approved list doesn't name.
- [ ] Row counts reconcile from raw to clean to ready-to-use, and every held row has a reason.
- [ ] A rerun adds nothing and changes nothing.
- [ ] When the AI hit a wall, it **stopped and asked** instead of quietly working around a rule.
- [ ] Every mistake the AI made is written down, with how it was caught.

**Gate:** I approve each slice before the next one starts.

## 4. Testing: test the tests

- [ ] Every test case passes, and a second full run gives identical results.
- [ ] Bugs were planted on purpose, one at a time. I know how many got through, and each gap now has a test.

**Gate:** I approve the test results.

## 5. Review and sign-off: a second pair of eyes

- [ ] A **second AI** reviewed the build against a written review brief.
- [ ] Every finding is fixed, accepted with a reason, or turned into a rule I approved.
- [ ] Each fix was proved with a test that failed first.
- [ ] Clean rebuild, rerun with matching results, and a walkthrough of every criterion.

**Gate:** I sign off each criterion by name. The build is done when I say it is.

---
From The Agentic Data Engineer starter kit. CC BY 4.0.
