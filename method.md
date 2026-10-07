# How it was built and checked

Every figure here is checked by a video's claims checker
before it appears on screen.

The idea is **See it sooner**: ask for something, see it built, review it, ask for changes and
see them sooner, with the phase gates kept. The videos do not quote durations or costs. My own numbers
for this build are below, with what they include, because a single number without context oversimplifies.

This series is a demonstration of what an AI coding assistant can do on a real build, explored on the side. It is not a benchmark, it does
not compare itself with a team, an estimate or another way of working, and it does not suggest a team is
not needed: the list below is what a real delivery still needs people for.

<!-- disclosed: what it took -->
## What it took me

These are my numbers for the claims recovery workbench, on synthetic data, while I was learning. They are not a benchmark, and
your numbers will differ with your setup, your data and how many times you go back and forth.

**Elapsed time, by phase.** Measured from my first prompt in a phase to the phase sign-off, using the
build's own log. It is wall-clock time: it includes stretches where the AI worked on its own, my reviews,
and breaks. It is not hands-on keyboard time.

| Phase | Elapsed |
| --- | --- |
| Requirements | 71 min |
| Design | 105 min |
| Development | 8.0 h |
| Unit testing, including the second-AI review and fixes | 18.4 h |
| Dry run | 1.0 h |
| **Total** | **about 30 h, over two days** |

**Databricks compute: $14.97**, at list price, read from Databricks' own billing table
(`system.billing.usage`) at the end of the dry run. It is a floor, because billing rows arrive late. The
workspace is a pay-as-you-go account with a small serverless SQL warehouse that stops after 5 minutes
idle. Nothing was scheduled.

**AI tools:** Claude Code built everything; Codex did the second review. Both are flat monthly
subscriptions, so this build had no per-token bill.

**Not included:** the time I spent learning the tools before the build, setting up the workspace, and
making the videos.
<!-- /disclosed -->

## What it didn't do (and what still needs people)

- **Subject matter experts.** The rules came from the requester; SMEs did not confirm them. The data is
  synthetic and built to represent claims, payments, contracts and payer rules.
- **Business users.** No user acceptance testing.
- **Production.** No QA environment, release, monitoring or on-call. The build stops at a dry run.
- **Security review.** No independent security or privacy review.
- **Scale testing.** Synthetic volumes only; performance at real hospital volume is untested.
- **A Genie space.** The build shipped an AI/BI dashboard; natural-language questions were not built.

## What was done to keep it honest

- Expected answers for every test case were locked in git before any code existed.
- 16 bugs were planted on purpose; the tests caught all 16.
- A second AI (Codex) reviewed the build independently; its 5 findings were fixed.
- Every mistake the AI made is logged and shown in the videos.
- Erum approved or redirected the work at every phase gate, and signed off all 18 acceptance criteria.
