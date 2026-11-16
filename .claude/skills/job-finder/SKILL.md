---
name: job-finder
description: Search the web for current job postings that match the skill profile in output/skills.json, verify and rank them by fit, and write the shortlist to output/jobs.md. Use when the user asks to find, search for or match jobs.
argument-hint: "[extra criteria, e.g. 'remote only' or 'in Sydney']"
context: fork
agent: general-purpose
allowed-tools: Read, Glob, Write, WebSearch, WebFetch
---

# Job Finder

Find real, currently open job postings that fit the candidate, rank them, and write
the shortlist to `output/jobs.md`.

Arguments (extra criteria for this run, they override stored preferences): `$ARGUMENTS`

## Input

1. Read `output/skills.json`. If it does not exist, stop and tell the user to run
   `/skill-extractor` first.
2. Read `config/preferences.md` if it exists.
3. Settle the search criteria: target roles, locations, work mode, seniority and any
   exclusions. Where a criterion is not stated anywhere, use the candidate's location
   and seniority from `skills.json` and say so in the report.

## Search

- Run a spread of searches rather than one: each of the `search_keywords`, each target
  role combined with each location (and with "remote" when the work mode allows it).
- Favour sources that link to the original posting: company career pages and applicant
  tracking systems (Greenhouse, Lever, Ashby, Workday, SmartRecruiters), then job
  boards that are strong in the candidate's region.
- Look for postings from the last 30 days. Include the current month and year in
  queries to bias towards fresh results.
- Collect 25 or more candidates before filtering.

## Verify

Open every posting you intend to list with WebFetch and confirm that:

- the page is a real job posting, not a search results page or an expired listing
- the title, company and location match what you will report
- it does not break a hard requirement (location, work mode, work rights, exclusions)

Drop anything you cannot open or confirm. **Never list a job from a search snippet
alone, and never invent a posting, a URL, a salary or a closing date.** If a board
blocks fetching, look for the same role on the company's own careers page.

Remove duplicates (the same role posted on several boards); keep the most direct link.

## Score

Score each verified posting out of 100:

| Factor | Weight | What to compare |
|---|---|---|
| Skill match | 45 | The posting's required skills against `skills`, weighted by level and recency |
| Seniority match | 20 | Level and years asked for against the candidate's |
| Role and domain fit | 15 | Title against `target_roles`, industry against `domains` |
| Preferences | 20 | Location, work mode, salary and other stated preferences |

Be honest about gaps: a required skill the candidate lacks lowers the score, and is
listed in the report.

## Output

Create `output/` if needed and write `output/jobs.md`:

```markdown
# Job Matches: <candidate name>

Searched: <date> · Criteria: <roles; locations; work mode> · Verified postings: <n>

## Shortlist
| # | Fit | Role | Company | Location / mode | Posted | Link |
|---|---|---|---|---|---|---|

## Details

### 1. <Role>, <Company> (<fit>/100)
- **Location / mode**:
- **Salary**: <as stated, or "not stated">
- **Posted / closes**: <as stated, or "not stated">
- **Why it fits**: <the candidate's skills and experience that match, specifically>
- **Gaps**: <required skills or experience the candidate lacks, or "none found">
- **Apply angle**: <which achievements from the CV to lead with for this role>
- **Link**: <url>

## Skill gaps seen across postings
<skills that several postings asked for and the candidate lacks; worth learning or
adding to the CV if they already have them>

## Search notes
<queries used, sources that were blocked, assumptions made about criteria>
```

List the top 10 to 15 by fit, best first. Leave out anything scoring under 50. If
fewer than five postings survive verification, say so plainly and suggest how to
widen the criteria instead of padding the list.

Finish by replying with the top five matches (role, company, fit, link), the recurring
skill gaps and the path of the report.
