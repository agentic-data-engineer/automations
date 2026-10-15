# A Second AI Checked the First One's Work

Video: link added when the video is published.

A second AI, Codex, reviewed what the first one built. It found five issues, including one nobody had
asked about. Then the dry run, and the sign-off.

## See it sooner

- **Ask for it.** Erum handed the build to Codex with a review brief. Exactly as typed:

  > Codex review completed. Please review brief.

- **See it.** Five findings. The first AI reproduced each one against the code: three were defects,
  fixed; two needed Erum's call.

## What the second AI caught

The worst one: a blank prior authorization, the insurer's OK before treatment, was read as a real value.
Like a blank signature read as signed. So a correct denial would have gone to staff as money to chase.

- **Change it.** Exactly as typed:

  > Approve R7 and R8, commit, and no need to run an additional focused review

  A new test (TC-34) was written first, failed exactly as reported, then passed once the fix landed:
  `nullif(trim(prior_auth_number), '')`.
- **Dry run.** A clean rebuild from scratch, a rerun with identical results across every table, and a
  walkthrough of all eighteen criteria.

## The gate

Exactly as typed:

> All 18 signed off, commit and close the phase

## Who does what

Two AIs built and reviewed it, and Erum approved every step that mattered. It's still a learning build on
made-up data. Taking it to production would need engineers, security, and billing staff testing it
themselves.

## What I learned

A second reviewer sees what the first one can't. People still sign off.

## Your turn

That's Build 1, start to finish. What should Erum automate next? Leave a comment on the video, or open a
discussion here.
