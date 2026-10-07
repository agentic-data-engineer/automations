# Episode 1: I Let an AI Agent Build a Data Product. Here's What I Learned.

Video: https://youtu.be/m9YRoMvB3TU

The whole build in about a minute: a coding assistant takes a hospital-claims data build on Databricks
through requirements, design, development, testing and a dry run. Erum sets the intent, makes the
calls and signs off. The assistant builds, tests and keeps the evidence.

## See it sooner

Ask for it. See it. Review it. Change it. See it again. And the gates stay.

Two of the prompts shown on screen, exactly as typed:

> ranks them by dollars at risk so staff work the biggest first

> What about a claim that's short by exactly one dollar?

The second one became a test case before any code existed. All the prompts are in
[the prompt pack](../prompt-pack.md).

## The gates

- Expected answers written and locked before any code
- 16 of 16 planted bugs caught by the tests
- A second AI's review findings fixed and re-tested
- 18 of 18 acceptance criteria signed off

## Still needs people

What a real delivery still needs a team for: data owners and real data, testing by business users,
engineers for production, security review, scale testing.

## Your turn

What would you poke holes in first? Leave a comment on the video, or open a discussion here.
