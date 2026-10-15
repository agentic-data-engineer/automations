# 16 Planted Bugs: Did the Tests Catch Them?

Video: link added when the video is published.

How do you know your tests would catch a mistake? Erum had the AI plant bugs on purpose, to test the
tests.

## See it sooner

- **Ask for it.** The AI broke its own logic twelve ways, one planted bug at a time, and reran every
  test each time. Erum asked what it was doing. Exactly as typed:

  > Explain exactly what this process is doing and what is the end goal

- **See it.** One bug made a claim count as underpaid only when it was short by more than a dollar. The
  one-dollar test (TC-30) was written for exactly that. Caught.

## What the planted bugs caught

Two got through: a filing-deadline rule that fired one day early, and an authorization rule that
ignored the authorization number. The tests had gaps.

- **Push back.** Exactly as typed:

  > Add TC-32 and TC-33, commit, and rerun mutations

- **See it again.** Twelve of twelve caught. Later, four more planted bugs for the review fixes: sixteen
  of sixteen caught.

## The gate

Every planted bug caught, and every test still passing on the real code.

## Who does what

The AI planted the bugs and ran the tests. In a real delivery, a QA team would bring its own test plan,
and business users would test it themselves.

## What I learned

Plant bugs on purpose. The two that slipped through showed exactly where to add tests.

## Your turn

What would you have pushed back on? Leave a comment on the video, or open a discussion here.
