---
name: cv-reviewer
description: Review a CV/resume like a recruiter and hiring manager would, and write actionable feedback to output/cv-review.md. Use when the user asks to review, critique, score or improve their CV or resume.
argument-hint: "[path to CV] [target role]"
context: fork
agent: general-purpose
allowed-tools: Read, Glob, Write
---

# CV Reviewer

Review the candidate's CV and write the review to `output/cv-review.md`.

Arguments: `$ARGUMENTS`

## Input

- If the arguments name a file, review that file.
- Otherwise use the most recently modified CV (PDF, DOCX or Markdown) in `profiles/`.
- If the arguments name a target role, review against that role. Otherwise infer the
  most likely target role from the CV and say which one you assumed.
- If `config/preferences.md` exists, read it for the target roles and seniority.

Read the whole CV before judging any part of it.

## How to review

Read it twice, as two different people:

1. **A recruiter with 10 seconds.** What do they learn from the top third of page one?
   Is the role, seniority and specialism obvious? Would they keep reading?
2. **A hiring manager with 5 minutes.** Do the bullets prove impact, or only list
   duties? Is there evidence for each claimed skill?

Then check each of these:

- **Impact**: bullets state an outcome, ideally with a number (scale, money, time,
  users, percent). Flag every bullet that only describes a responsibility.
- **Relevance**: the content matches the target role; old or unrelated material is
  trimmed.
- **Clarity**: strong verbs, no filler, no unexplained internal jargon, consistent tense.
- **Structure**: sensible section order, reverse-chronological, length suits the
  seniority, dates and titles are consistent, no unexplained gaps.
- **ATS-friendliness**: plain headings, text that survives parsing, no content locked
  in tables, columns or images; the keywords of the target role appear naturally.
- **Hygiene**: spelling, grammar, contact details, working links.

## Rules

- Be specific. Quote the line you are criticising and show a rewritten version.
- Never invent achievements, numbers or skills. When a bullet needs a metric you do not
  have, write the rewrite with a placeholder such as `[X%]` and ask the candidate for it.
- Say what is strong as well as what is weak; the candidate needs to know what to keep.
- Order fixes by how much they would improve the chance of an interview.

## Output

Create `output/` if needed and write `output/cv-review.md` in this shape:

```markdown
# CV Review: <candidate name>

Reviewed file: <path> · Target role: <role> (stated | assumed) · Date: <today>

## Verdict
<2-3 sentences: would this CV get an interview for the target role, and why>

## Scores
| Area | Score /10 | One-line reason |
|---|---|---|
| Impact | | |
| Relevance | | |
| Clarity | | |
| Structure | | |
| ATS-friendliness | | |
| **Overall** | | |

## Strengths
- ...

## Top fixes (highest value first)
1. **<fix>**: <why it matters>
   - Before: "<quoted line>"
   - After: "<rewrite>"

## Section-by-section notes
### <section name>
- ...

## Questions for the candidate
- <missing metric or fact that would strengthen a specific bullet>
```

Finish by replying with the verdict, the overall score, the top three fixes and the
path of the review file.
