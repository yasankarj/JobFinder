# JobFinder

An agentic job-finding pipeline built from Claude Code skills.

This repo holds two separate things. Keep them separate.

| | The product | The development process |
|---|---|---|
| What | The job-finding skills that are this project's deliverable | AI-DLC, the workflow that governs how the product is built |
| Lives in | `.claude/skills/`, `config/` | `aidlc-workflows/` (rules, git-ignored), `aidlc-docs/` (generated artifacts) |
| Used when | A user runs `/find-jobs` and the other product commands | Someone asks for development work on this repo |

- Running a product skill (`/cv-reviewer`, `/find-jobs`, ...) is using the product, not
  development work. Do not start the AI-DLC workflow for it.
- Never put AI-DLC rules, stages, questions or artifacts into a product skill, and
  never put product behaviour into `aidlc-workflows/` or `aidlc-docs/`.
- `.claude/skills/` is for product skills only. If development work ever needs its own
  skill or agent, do not add it there without asking where it should go.
- Do not edit anything in `aidlc-workflows/`; it is an unmodified upstream copy.

## The product

| Command | What it does | Writes |
|---|---|---|
| `/cv-reviewer [cv] [role]` | Reviews the CV as a recruiter and hiring manager would | `output/cv-review.md` |
| `/skill-extractor [cv]` | Extracts a structured skill profile | `output/skills.json` |
| `/job-finder [criteria]` | Web-searches, verifies and ranks postings against the profile | `output/jobs.md` |
| `/find-jobs [cv] [criteria]` | Runs all three in order | all of the above |

- Each skill runs as a forked subagent (`context: fork`) and hands off through files in
  `output/`, so any step can be inspected, edited and re-run alone.
- Inputs: the CV in `profiles/` (git-ignored) and search preferences in
  `config/preferences.md` (versioned).
- `output/skills.json` is the contract between `skill-extractor` and `job-finder`; if
  its schema changes, update both skills.
- The CV and everything in `output/` are personal data; both folders are git-ignored.
  Do not commit them or send them to outside services beyond the job searches
  themselves.

## Commits

Follow [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/):
`type: summary`, on one line, lower-case imperative summary, no trailing full stop.

- Types: `feat` (new skill or capability), `fix` (wrong behaviour corrected), `docs`
  (README, CLAUDE.md, `aidlc-docs/`), `refactor`, `chore` (git/config housekeeping),
  `test`.
- A change to a product skill's instructions is `feat` or `fix`, not `docs`: SKILL.md
  files are the product's behaviour.
- Mark a breaking change with `!` (`feat!: ...`), for example a change to the
  `output/skills.json` schema.
- One logical change per commit.

## Development workflow: AI-DLC

All development work on this repo (new or changed skills, features, fixes, refactors)
follows AI-DLC v1 (AI-Driven Development Life Cycle,
https://github.com/awslabs/aidlc-workflows, `v1` branch, rules version 1.0.1). The core
workflow is imported at the end of this file.

- **AI-DLC is the default; no trigger phrase is needed.** Treat any request that would
  change what the product does as an AI-DLC request, whether or not the user says
  "Using AI-DLC". That covers adding, changing, fixing or refactoring a product skill,
  changing `config/` or the `output/` file contracts, and adding any new capability.
  Start the workflow (or resume it from `aidlc-docs/aidlc-state.md`) before writing
  any of it.
- **Not development work, so no workflow**: running a product skill, answering
  questions or explaining the code, git operations (commits, branches, ignore rules),
  and wording-only edits to `README.md` or this file.
- If it is unclear which side a request falls on, ask once before starting.
- The user can skip the workflow for a single request by saying so (for example
  "skip AI-DLC" or "quick fix"). Do not skip it on your own judgment because a change
  looks small; the workflow already scales its depth to the size of the change.
- **Rule details directory**: `aidlc-workflows/aidlc-rules/aws-aidlc-rule-details/`.
  Use this path wherever the core workflow tells you to resolve the rule details
  directory; the candidate paths it lists do not exist in this repo.
- **Artifacts** go in `aidlc-docs/` at the repo root, as the workflow specifies. They
  are versioned.
- **If `aidlc-workflows/` is missing** (it is git-ignored, so a fresh clone will not
  have it), stop and ask the user to restore it as described in the README. Do not do
  development work from memory of the workflow.
- Where AI-DLC and this file disagree on commits or on the product/process separation
  above, this file wins.

@aidlc-workflows/aidlc-rules/aws-aidlc-rules/core-workflow.md
