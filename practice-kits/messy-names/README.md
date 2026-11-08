# Try it yourself · I Asked AI To Clean These Messy Names

**The problem.** A customer list has names in every style: all caps, stray spaces, Mc and O' names, surname-first entries, an initial, a single name and a blank. A careless fix silently breaks names.

**Tool and access.** Claude.ai in a desktop browser, Pro plan, model shown as Opus 5.5 Medium, tested. File upload and Claude.ai's code execution were used. The free plan is not tested.

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

- Summary versus table: Claude's reply says it flagged 7 names. The table flags 8 rows. The eighth is C017, which has no name at all, so 7 names + 1 blank row = 8 flagged rows. Both numbers are right and describe different things.
- My answer key would also have flagged C006 (McDonald) and C010 (Jr.). Claude did not flag them, and both names match the key.
- Three names differ from the key (C008, C016, C020). All three are flagged for a person.
- The tested prompt does not ask Claude to split names, so the Short does not claim splitting.
- AI output can differ between runs. Check it before relying on it.

In expected_output.csv, dashes in Claude's review notes were changed to hyphens for this page. Nothing else in the file was changed.
