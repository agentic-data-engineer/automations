# One Question Changed the AI's Whole Design

Video: link added when the video is published.

Before building anything, Erum asked the AI one question. It changed the whole design.

## See it sooner

- **Ask for it.** Exactly as typed:

  > Run the design phase.

  The AI read the approved requirements, checked the workspace without changing anything, and asked
  seven design questions, each answered in a line.
- **See it.** The draft: raw data lands, gets cleaned, then scored (bronze, silver, gold). Every
  source field mapped, and every table listed before anything is created.
- **Review it.** One question:

  > Also are you creating SDP pipelines?

- **Change it.** The AI compared the options, and the design became Spark Declarative Pipelines (SDP):
  the tables are declared, and the pipeline handles the order, the quality checks and the run history.
- **Review it again.** Exactly as typed:

  > Review once again with best practices for databricks and then apply all gaps before proceeding with build

- **Change it.** The AI closed its own gaps: a landing contract, standard columns, a reason for every
  held record, rerun rules, and access. And it split the work in two: one pipeline lands the data, one
  cleans and scores it.

## What the AI caught

The AI said AUTO CDC, the Spark Declarative Pipelines feature for change data capture, matched one of the
rules exactly: when records change or arrive late, keep the latest. Before building, it checked the docs.
When two records tie, that feature quietly picks a winner using a tiebreaker column. The approved rule
is different: conflicting records go to a person for review (two locked test cases cover it). So the
AI kept those rules in its own view and did not use the feature. The reasoning is in the design, section
3.2: [design](deliverables/design/design.md).

## The deliverables

| Deliverable | What it is |
| --- | --- |
| [Design document](deliverables/design/design.md) | The approved design: architecture decision, data flow, landing contract, source-to-target map, rules and reason codes, gold model, reruns, access, and the list of everything to be built |
| [Design decisions](deliverables/design/decisions.md) | The questions the AI asked and the answers, including the two review rounds |

Left out of the published design: the sources consulted (outside links), the workspace check (account
details) and the cost guardrails.

## Who does what

The AI drafted the design and checked it against the docs. A real delivery still needs architects to
review it against company standards, and security to approve who can see what.

## What I learned

One question about the platform reshaped the design. Ask the AI to review its own work: it found gaps I
didn't think to ask about.

## Your turn

How would you automate this? Leave a comment on the video, or open a discussion here.
