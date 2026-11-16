# Application Design Plan

Sources: `requirements.md`, `stories.md`, `execution-plan.md`, and the Claude Code skills
documentation (https://code.claude.com/docs/en/skills.md).

## Platform Facts That Constrain the Design
- `allowed-tools` **pre-approves** tools for the skill's turn. It does **not** block other
  tools, which stay subject to the user's permission settings (they prompt). To
  restrict the skills to only the commands they need (SECURITY-06), the pre-approved
  command should be as narrow as possible.
- `${CLAUDE_PROJECT_DIR}` and `${CLAUDE_SKILL_DIR}` can be used in SKILL.md and in
  `allowed-tools` Bash rules, for example `Bash(python3 ${CLAUDE_PROJECT_DIR}/lib/x.py *)`.
- Sharing one file across standalone skills is not documented. A file that skills read by
  project path works. A folder under `.claude/skills/` without `SKILL.md` has
  undocumented behaviour.
- Whether a forked skill can invoke other skills is not documented. The orchestrator
  `find-jobs` therefore stays inline (not forked), as today. The Skill tool takes `args`,
  so it can pass candidate IDs to the worker skills.
- The workers today may use only Read, Glob and Write. Write cannot move a binary file
  (PDF, DOCX), so migration needs a shell command.

## Questions

Fill in each `[Answer]:` with a letter, or choose Other and describe what you want.

## Question 1
How should the shared candidate rules be implemented? These are: listing candidates, validating IDs, resolving arguments, finding loose CVs and moving files without overwriting.

A) A small Python 3 script (standard library only) does the mechanical work: list, validate, make IDs from names, move without overwriting, and report as JSON. A short shared Markdown file covers the steps Claude itself must do, such as reading the name from a CV. Skills pre-approve only `python3 <script> *`. The rules can be tested, so property-based testing applies (with Hypothesis), and the NFR stages run. Requires `python3` on the machine.

B) A shared Markdown file only. Claude follows it in each skill, and skills pre-approve `mkdir -p *` and `mv -n *` for moves. There's no code and no tests, but the result depends on Claude following the instructions exactly. The pre-approval is broader: any `mv -n` anywhere.

C) Copy the rules into each SKILL.md. There's no shared file, but four copies must be kept in sync (this goes against NFR-4.1).

X) Other (please describe after [Answer]: tag below)

[Answer]: 

## Question 2
Where should the shared files live?

A) A new top-level `lib/` folder, for example `lib/candidates.md` (and `lib/candidates.py` if Q1 is A), read by path. `CLAUDE.md` and `README.md` list `lib/` as part of the product.

B) Under `.claude/skills/shared/`, with no `SKILL.md`. This keeps all product behaviour under `.claude/skills/`, but Claude Code's handling of such a folder is undocumented.

C) Inside the `find-jobs` skill folder, with the other skills reading it from there

X) Other (please describe after [Answer]: tag below)

[Answer]: 

## Question 3
How should `/find-jobs` run several candidates?

A) One candidate at a time. For each candidate it invokes `cv-reviewer`, `skill-extractor` and `job-finder` in turn, each as its own forked agent, so each agent only ever sees one candidate's data. That's three agents per candidate.

B) One step at a time. It invokes each skill once with all the IDs, and the skill loops over candidates inside one agent. Fewer agents, but one agent's context holds several candidates' data.

X) Other (please describe after [Answer]: tag below)

[Answer]: 

## Question 4
When a worker command is run on its own with several IDs (for example `/cv-reviewer jane-doe john-smith`), it runs as one forked agent. How should it handle the candidates?

A) Loop inside that one agent, finishing and writing each candidate's results before reading the next candidate's files. This is simple, but the agent's context holds more than one candidate.

B) Accept only one ID when run on its own, and point the user to `/find-jobs` for batches. This keeps strict isolation, but it narrows US-2.3 for the worker commands.

X) Other (please describe after [Answer]: tag below)

[Answer]: 

## Execution Checklist (after answers are approved)
- [ ] Generate `aidlc-docs/inception/application-design/components.md` with component definitions and high-level responsibilities
- [ ] Generate `aidlc-docs/inception/application-design/component-methods.md` with method signatures (business rules detailed later in Functional Design)
- [ ] Generate `aidlc-docs/inception/application-design/services.md` with service definitions and orchestration patterns
- [ ] Generate `aidlc-docs/inception/application-design/component-dependency.md` with dependency relationships and communication patterns
- [ ] Generate `aidlc-docs/inception/application-design/application-design.md` consolidating the above
- [ ] Validate design completeness and consistency against requirements and stories
- [ ] Security compliance review (SECURITY-05, 06, 08, 11, 13, 15 in particular)
- [ ] Update `execution-plan.md` with the NFR stage decision (run or skip) based on Q1
