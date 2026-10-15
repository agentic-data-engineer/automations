# My Top Requirement Had Zero Tests

Video: link added when the video is published.

The ready-to-use layer and the dashboard. The thing Erum asked for most was quietly passing with nothing
behind it, until the acceptance check refused to pass it.

## See it sooner

- **Ask for it.** The ready-to-use layer: tables that score every claim line, and a dashboard for the
  billing staff.
- **See it.** Built like a star: one central table of claim lines, with payers, procedures, contract
  rates and rules around it, scored and ranked.
- **Review it.** The AI proposed the dashboard: page one, the ranked worklist; page two, the evidence;
  page three, totals and the review queue. Exactly as typed:

  > Approve tiles, yes read both files, commit and proceed.

## What the acceptance check caught

Erum's top ask was the evidence behind every result (criterion AC-10). No test case mapped to it. The
acceptance check found zero cases and refused to pass it: zero results is a failure, not a pass. Now the
evidence is checked on every flagged line.

## The gate

Every figure on the dashboard ties back to the tables.

## Who does what

The AI built the tables, the dashboard and the checks. In a real delivery, billing staff would try it on
their own work, and a reporting team would review the design.

## What I learned

A requirement with no test isn't being checked. Make zero results fail.

## Your turn

What would you have pushed back on? Leave a comment on the video, or open a discussion here.
