# Starter prompts

Five prompts, one per phase. Each one tells the AI what to draft and where to stop. Replace the
bracketed parts. Use synthetic data unless you have written approval to use real data.

## 1. Requirements

> Let's start the requirements phase for a new data product. Don't write any pipeline code yet.
>
> The problem: [the business problem in plain words: who is struggling, with what, and why it costs them].
> The users: [who uses the result and what decision they make with it].
> It must never: [actions that stay with people, e.g. submit, approve, pay, delete].
> Sources: [the systems or files the data comes from].
> Data rules: synthetic data only.
>
> Read it back to me, list what's ambiguous, and ask me numbered questions. Then draft acceptance
> criteria, each with a pass or fail. Stop and wait for my approval at each step.

Then, after the criteria are approved:

> Answer key first: propose test cases with expected results written by hand, mapped to the criteria.
> Include duplicates that hide problems, values at every threshold, rates that change over time,
> missing references, late changes, invalid input, and records that must not be flagged. Show the
> arithmetic. Commit the answer key on its own before any code exists.

## 2. Design

> Start the design phase from the approved requirements. Check the workspace read-only first; change
> nothing. Fetch the current platform docs for every feature you plan to use and cite them. Bring me
> every open choice as options with a recommendation. Then list every artifact you will create, with
> full names, and a spend ceiling. Stop for my approval before anything is built.

When the draft comes back, push on it:

> Review this design again against best practices. What did you leave open, and what would a senior
> engineer question?

## 3. Build

> Build only [the slice, e.g. raw layer and the test-data loader], exactly as the approved design lists.
> After deploying, compare what exists with the approved artifact list. Reconcile row counts between
> layers and explain every held row. Run it twice and show the second run changed nothing. If you hit
> something the design didn't plan for, stop and ask me; don't work around it.

## 4. Testing

> Run every test case and write an evidence file per criterion with PASS or FAIL. Then test the tests:
> change the core logic in small ways, one at a time, and run the tests against each change. Tell me
> which changes the tests did not catch.

## 5. Review and sign-off

> Write a review brief for an independent AI reviewer: what to check, against which requirements, and
> where the evidence is. Don't fix anything yet.

After the second AI's findings come back:

> Here are the reviewer's findings. Check each one against the code. For each: fixed, accepted with a
> reason, or a new rule for me to approve. For every fix, write the failing test first.

Then:

> Dry run: rebuild from clean tables, rerun, and walk me through every acceptance criterion with its
> evidence. I'll sign off each one by name.

---
From The Agentic Data Engineer starter kit. CC BY 4.0.
