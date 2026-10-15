# One Word in the Output Gave the Bug Away

Video: link added when the video is published.

The build starts. Every step ended with a check that passed, and one word in the output still gave a
bug away.

## See it sooner

- **Ask for it.** With the design approved, the AI starts the build. It could not create the catalog,
  and the project's rule says don't work around it with a fallback. So it stopped, and Erum created the
  catalog herself. Exactly as typed:

  > Catalog created, proceed with bronze slice

- **See it.** The raw layer: files land in seven tables, exactly as they arrived, about two thousand
  made-up claims and six thousand claim lines.
- **Ask again.** Exactly as typed:

  > Proceed with silver

- **See it.** The cleaned layer: standard names and types, duplicates collapsed, and every bad record
  held aside with a reason. None silently dropped.

## What the rerun caught

On a rerun, the loader printed "uploaded" where it should have said the file was already there,
identical. The guard on the raw files was not really checking. Nothing had changed that time, so no data
was affected. Now an identical file is skipped and a changed file is refused.

## The gate

Counts tie from raw to clean, and every held record is explained.

## Who does what

The AI wrote and ran the build and the checks. In a real delivery, data engineers would review the code,
and a platform admin would own the workspace setup.

## What I learned

Read the output, not just the pass. One word gave the bug away.

## Your turn

What would you have pushed back on? Leave a comment on the video, or open a discussion here.
