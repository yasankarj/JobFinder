# JobFinder

An agentic job-finding pipeline built from [Claude Code](https://claude.com/claude-code)
skills. Give it a CV and it reviews the CV, extracts a skill profile, then searches the
web for matching jobs and ranks them by fit.

## How it works

```
profiles/<cv>.pdf ──┬─► cv-reviewer ─────► output/cv-review.md
                    │
                    └─► skill-extractor ─► output/skills.json ─► job-finder ─► output/jobs.md
                                                                     ▲
config/preferences.md ───────────────────────────────────────────────┘
```

Each skill runs as its own subagent and hands its result to the next through a file in
`output/`, so any step can be inspected, edited and re-run alone.

| Skill | What it does | Writes |
|---|---|---|
| `cv-reviewer` | Reviews the CV as a recruiter and a hiring manager would: scores, strengths, and prioritised fixes with before/after rewrites | `output/cv-review.md` |
| `skill-extractor` | Extracts a structured profile: skills with level, years and evidence, plus target roles and search keywords | `output/skills.json` |
| `job-finder` | Searches the web for current postings, opens each one to verify it, and scores it out of 100 for fit | `output/jobs.md` |
| `find-jobs` | Runs the three skills above in order and gives one summary | all of the above |

## Requirements

- Claude Code, with web search and web fetch available (used by `job-finder`)

No API keys, dependencies or build step: the skills are Markdown instructions in
`.claude/skills/`.

## Usage

1. Put your CV (PDF, DOCX or Markdown) in `profiles/`.
2. Optionally fill in `config/preferences.md` with target roles, locations, work mode
   and salary. Anything left blank is inferred from the CV.
3. Open Claude Code in this folder and run:

   ```
   /find-jobs
   ```

Or run the steps one at a time:

```
/cv-reviewer [path to CV] [target role]
/skill-extractor [path to CV]
/job-finder [extra criteria, e.g. "remote only"]
```

With no CV path, the skills use the most recently modified CV in `profiles/`.

## Development

Development on this repo follows
[AI-DLC](https://github.com/awslabs/aidlc-workflows) (AI-Driven Development Life
Cycle), v1. It is a structured workflow, Inception then Construction then Operations,
in which Claude asks clarifying questions, writes a plan, and waits for approval at
each stage before building.

### Product and process are kept apart

| | The product | The development process |
|---|---|---|
| What | The job-finding skills | AI-DLC rules and the artifacts they produce |
| Lives in | `.claude/skills/`, `config/` | `aidlc-workflows/`, `aidlc-docs/` |
| Used when | You run `/find-jobs` and the other commands | You ask for a change to this repo |

`.claude/skills/` holds product skills only. The guidelines and agents used to build
the product never go there, and product behaviour never goes into the AI-DLC folders.

### Setup

`aidlc-workflows/` is git-ignored, so each clone needs its own copy:

1. Download the `v1` branch of
   [awslabs/aidlc-workflows](https://github.com/awslabs/aidlc-workflows/tree/v1) as a
   zip (this project uses rules version 1.0.1).
2. Extract it to `aidlc-workflows/` in the repo root, so that
   `aidlc-workflows/aidlc-rules/aws-aidlc-rules/core-workflow.md` exists.

`CLAUDE.md` imports that core workflow and points it at the rule details in
`aidlc-workflows/aidlc-rules/aws-aidlc-rule-details/`. No files are copied out of the
folder.

### Working with it

AI-DLC is the default: just ask for the change, with no trigger phrase. For example:

```
Add a skill that drafts a cover letter for a job in output/jobs.md
```

Any request that changes what the product does (a new or changed skill, a fix, a
refactor, a change to `config/` or the output files) starts or resumes the workflow.
Running the product, asking questions, git housekeeping and wording-only doc edits do
not. To bypass it for one request, say "skip AI-DLC".

Answer the questions it asks, review each plan, and approve each stage. The artifacts
(requirements, designs, plans, audit log) are written to `aidlc-docs/` and are
versioned.

Commits follow [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
as a single `type: summary` line.

## Project layout

```
.claude/skills/             the product
  cv-reviewer/SKILL.md
  skill-extractor/SKILL.md
  job-finder/SKILL.md
  find-jobs/SKILL.md        orchestrator
config/preferences.md       search preferences (versioned)
profiles/                   your CV (git-ignored)
output/                     generated results (git-ignored)
aidlc-workflows/            AI-DLC v1 rules (git-ignored, see Development)
aidlc-docs/                 AI-DLC artifacts, created on first development request
CLAUDE.md                   instructions for Claude Code; imports the AI-DLC workflow
```

## Privacy

`profiles/` and `output/` are git-ignored because they hold personal data.
`config/preferences.md` is versioned, so check what you write there before pushing the
repo anywhere public.

## Design rules

- The reviewer never invents achievements, numbers or skills; where a metric is missing
  it leaves a placeholder and asks for it.
- The extractor records only skills the CV gives evidence for.
- The job finder lists only postings it has opened and confirmed, and reports honestly
  when few survive verification.
