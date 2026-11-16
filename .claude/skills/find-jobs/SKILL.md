---
name: find-jobs
description: Run the whole job-finding pipeline, CV review, skill extraction, then job search, and summarise the results. Use when the user wants the full run from CV to job shortlist.
argument-hint: "[path to CV] [extra search criteria]"
---

# Find Jobs (pipeline)

Run the three job-finding skills in order. Each one runs as its own agent and hands
its result to the next through a file in `output/`.

Arguments: `$ARGUMENTS`

| Step | Skill | Reads | Writes |
|---|---|---|---|
| 1 | `cv-reviewer` | CV in `profiles/` | `output/cv-review.md` |
| 2 | `skill-extractor` | CV in `profiles/` | `output/skills.json` |
| 3 | `job-finder` | `output/skills.json` | `output/jobs.md` |

## Steps

1. Confirm there is a CV in `profiles/` (or one named in the arguments). If there is
   none, stop and ask the user to add one.
2. If `config/preferences.md` is still the unedited template, mention once that
   filling it in will sharpen the search, then carry on.
3. Invoke `cv-reviewer` with the Skill tool, passing the CV path if one was given.
4. Invoke `skill-extractor` the same way.
5. Check that `output/skills.json` exists and parses as JSON. If it does not, fix or
   re-run step 4 before continuing.
6. Invoke `job-finder`, passing any search criteria from the arguments.
7. Give the user one summary:
   - CV verdict, overall score and the top three fixes
   - the profile in one line (seniority, top skills, target roles)
   - the top five job matches with fit score and link
   - skill gaps that came up repeatedly
   - the paths of the three output files

Report a failed step as failed; do not fill in for it from memory.
