# JobFinder

An agentic job-finding pipeline built from Claude Code skills in `.claude/skills/`.

| Command | What it does | Writes |
|---|---|---|
| `/cv-reviewer [cv] [role]` | Reviews the CV as a recruiter and hiring manager would | `output/cv-review.md` |
| `/skill-extractor [cv]` | Extracts a structured skill profile | `output/skills.json` |
| `/job-finder [criteria]` | Web-searches, verifies and ranks postings against the profile | `output/jobs.md` |
| `/find-jobs [cv] [criteria]` | Runs all three in order | all of the above |

- Each skill runs as a forked subagent (`context: fork`) and hands off through files in
  `output/`, so any step can be inspected, edited and re-run alone.
- Inputs live in `profiles/`: the CV, plus `preferences.md` for search preferences.
- `output/skills.json` is the contract between `skill-extractor` and `job-finder`; if
  its schema changes, update both skills.
- The CV and everything in `output/` are personal data. Do not commit them or send
  them to outside services beyond the job searches themselves.
