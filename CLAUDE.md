# JobFinder

An agentic job-finding pipeline built from Claude Code skills in `.claude/skills/`.

| Command | What it does | Writes |
|---|---|---|
| `/cv-reviewer [cv] [role]` | Reviews the CV as a recruiter and hiring manager would | `output/cv-review.md` |
| `/skill-extractor [cv]` | Extracts a structured skill profile | `output/skills.json` |
| `/job-finder [criteria]` | Web-searches, verifies and ranks postings against the profile | `output/jobs.md` |
| `/find-jobs [cv] [criteria]` | Runs all three in order | all of the above |

## Commits

Follow [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/):
`type: summary`, on one line, lower-case imperative summary, no trailing full stop.

- Types: `feat` (new skill or capability), `fix` (wrong behaviour corrected), `docs`
  (README, CLAUDE.md), `refactor`, `chore` (git/config housekeeping), `test`.
- A change to a skill's instructions is `feat` or `fix`, not `docs`: SKILL.md files are
  the product's behaviour.
- Mark a breaking change with `!` (`feat!: ...`), for example a change to the
  `output/skills.json` schema.
- One logical change per commit.

## Notes

- Each skill runs as a forked subagent (`context: fork`) and hands off through files in
  `output/`, so any step can be inspected, edited and re-run alone.
- Inputs: the CV in `profiles/` (git-ignored) and search preferences in
  `config/preferences.md` (versioned).
- `output/skills.json` is the contract between `skill-extractor` and `job-finder`; if
  its schema changes, update both skills.
- The CV and everything in `output/` are personal data; both folders are git-ignored.
  Do not commit them or send them to outside services beyond the job searches
  themselves.
