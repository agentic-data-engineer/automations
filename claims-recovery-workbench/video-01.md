# An AI Built My Databricks Data Product. I Approved Every Step That Mattered.

Video: https://youtu.be/XfuCTJpVZoY

The whole build: an AI coding assistant takes a hospital-claims data product on Databricks through
requirements, design, build, testing and a dry run. Erum describes what she wants, sees it working, and
pushes back. She approved every step that mattered.

## The loop

Every phase runs the same loop: ask, see it, push back, ask again. Then a gate before the next phase.

Two of the prompts shown on screen, exactly as typed:

> Run the design phase.

> Also are you creating SDP pipelines?

The second one changed the whole design: the AI compared the options and chose Spark Declarative
Pipelines. All the prompts are in [the prompt pack](prompts.md).

## What got built

- The expected answers, locked before any code
- Two pipelines and twenty-three tables, across the raw, cleaned and ready-to-use layers
- A ranked worklist of denied and underpaid claims

## What the second AI caught

A blank prior authorization, the insurer's OK before treatment, was read as a real value. So a correct
denial would have gone to staff as money to chase. A new test failed first, then the fix made it pass:
`nullif(trim(prior_auth_number), '')`.

## The gates

- Expected answers written and locked before any code
- 16 of 16 planted bugs caught by the tests
- A second AI's review findings fixed and re-tested
- 18 of 18 acceptance criteria signed off

## What this is, and isn't

A learning build, on made-up data. It's not production. A real delivery would still need data engineers,
the billing team, and security.

## Your turn

What would you have pushed back on? Leave a comment on the video, or open a discussion here.
