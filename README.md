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

## Project layout

```
.claude/skills/
  cv-reviewer/SKILL.md
  skill-extractor/SKILL.md
  job-finder/SKILL.md
  find-jobs/SKILL.md        orchestrator
config/preferences.md       search preferences (versioned)
profiles/                   your CV (git-ignored)
output/                     generated results (git-ignored)
CLAUDE.md                   notes for Claude Code
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
