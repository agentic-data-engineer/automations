# Answer key first

A free skill for AI coding assistants (Claude Code and others that read skill files): before the AI
writes any pipeline, report or data product, it helps you define what "right" looks like and locks it.

**What it does:** turns your business request into testable acceptance criteria, then test cases with
expected results written by hand, covering the edge cases people usually miss (rates that change over
time, duplicates that hide problems, values exactly at a threshold, late changes, invalid input). It
stops for your approval at every step and commits the answer key before any code exists.

**Why:** if the answer is written after the code runs, the test can only agree with the code. Written
first, it can catch the code being wrong.

## Install (Claude Code)

Copy the `answer-key-first` folder into your project's `.claude/skills/` folder (or your personal
`~/.claude/skills/`). Then start with:

> Answer key first: [describe your business problem in plain words]

## Files

| File | What it is |
| --- | --- |
| `SKILL.md` | The skill: rules, gates and steps |
| `example.md` | A worked slice from a synthetic hospital-claims build |

Works with any data platform. Use synthetic data unless you have approval to use real data.

From The Agentic Data Engineer. Licensed CC BY 4.0.
