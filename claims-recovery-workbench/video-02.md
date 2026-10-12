# I Made the AI Write the Answer Key Before Any Code

Video: https://youtu.be/ig2C0KAuOps

Before any code, Erum had the AI write the answer key: test cases with their expected results, locked
in git, so the code is judged against answers it never wrote.

## See it sooner

- **Ask for it.** Her first prompt, in plain words: a person makes every decision; the tool never
  submits, appeals or collects; and don't build anything yet.
- **See it.** The AI read it back and asked before it assumed: 19 decisions, each answered in a line.
  Then 18 acceptance criteria, each pass or fail, and 29 test cases with expected answers, locked
  before any code.
- **Review it.** Erum poked holes, exactly as typed:

  > What about a duplicate payment that hides an underpayment? What about a claim that's short by
  > exactly one dollar? What about a contract rate that changes partway through the year?

- **Change it.** Two of her three were already covered. The third became test 30, with its expected
  answer, added before any code.

## What the AI caught

The AI's first draft of one criterion allowed real procedure codes. The project's rule was synthetic
data only. The AI spotted the mismatch on its own and switched to made-up codes.

## The deliverables

What the requirements phase handed over:

| Deliverable | What it is |
| --- | --- |
| [Use case](deliverables/requirements/use-case.md) | The request, restated and confirmed |
| [Decisions](deliverables/requirements/decisions.md) | The questions the AI asked and the answers |
| [Acceptance criteria](deliverables/requirements/acceptance-criteria.md) | 18 criteria, each pass or fail |
| [Answer key excerpt](deliverables/requirements/answer-key-excerpt.md) | Two of the locked test cases: the duplicate and the one-dollar case |

Assembled afterwards from those four, for a regulated-style review: the
[requirements and traceability workbook](deliverables/requirements/requirements-traceability.md)
([.xlsx](deliverables/requirements/requirements-traceability.xlsx)). User, functional and
non-functional requirements, each with a testable pass condition, traced to the criteria and the tests
that proved it. Eight requirements are marked OPEN: security and operations a real deployment would
need. Draft, not yet approved.

## Who does what

The AI drafted the whole answer key. A real delivery still needs subject matter experts to confirm the
rules, and end users (billing staff) to confirm it fits their work. Neither reviewed this build.

## What I learned

Poke holes before any code. Two of my three cases were already covered. The third became a test.

## Your turn

How would you automate this? Leave a comment on the video, or open a discussion here.
