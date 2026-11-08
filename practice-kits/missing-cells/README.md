# Try it yourself · Why Didn't AI Fill This Blank?

**The problem.** A report has blank category cells. Filling every blank from the row above puts values on rows they do not belong to.

**Tool and access.** Claude.ai in a desktop browser, Pro plan, model shown as Opus 5.5 Medium, tested 2026-11-08. File upload and Claude.ai's code execution were used. The free plan is not tested.

## Steps

1. Download sample_input.csv from this folder.
2. Open https://claude.ai and start a new chat.
3. Attach sample_input.csv.
4. Paste the prompt from exact_prompt.md and send.
5. Compare the CSV it returns with expected_output.csv. Results can vary between runs.

## Files

| File | What it is |
| --- | --- |
| sample_input.csv | The synthetic practice file |
| exact_prompt.md | The verbatim prompt tested |
| expected_output.csv | The verified result from the recorded run |
| validation.md | Checks behind every number |
| source_and_permissions.md | Sources and sharing notes |

## Limits

- 18 of 18 means all 18 rows of this practice file matched the answer key: 6 filled, 9 left blank on purpose, 3 categories already present. It says nothing about other files.
- The tested prompt never names the gift card. It gives a general rule, and Claude applied it on this file. This does not show it knows every business rule.
- The orange "Software?" cell is an illustration of what a fill-down would guess, labelled on screen. It is not AI output.
- The tested prompt asks for a note on every fill and to leave other blanks alone. It does not use the word flag.
- AI output can differ between runs. Check it before relying on it.
