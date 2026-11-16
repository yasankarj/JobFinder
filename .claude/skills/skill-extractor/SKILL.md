---
name: skill-extractor
description: Extract a structured skill profile (skills, experience level, domains, target roles, search keywords) from a CV/resume and write it to output/skills.json. Use when the user asks to extract, list or summarise the skills in their CV, or before searching for jobs.
argument-hint: "[path to CV]"
context: fork
agent: general-purpose
allowed-tools: Read, Glob, Write
---

# Skill Extractor

Turn the candidate's CV into a structured profile that the `job-finder` skill can
search with. Write it to `output/skills.json`.

Arguments: `$ARGUMENTS`

## Input

- If the arguments name a file, use it. Otherwise use the most recently modified CV
  (PDF, DOCX or Markdown) in `profiles/`.
- If `profiles/preferences.md` exists, read it. Stated preferences override anything
  you would infer from the CV (target roles, locations, seniority).

## How to extract

- **Only record what the CV supports.** Every skill needs evidence: the role, project
  or certification where it appears. Do not add skills because they usually go together
  with ones that are listed.
- **Normalise names** to the form job adverts use ("JS" becomes "JavaScript", "k8s"
  becomes "Kubernetes"), and merge duplicates.
- **Estimate depth honestly** from the evidence:
  - `expert`: used across several roles or years, led work with it, or is central to
    the current role
  - `proficient`: used in real work in at least one role
  - `familiar`: mentioned only in a skills list, coursework or a side project
- **Years** come from the dates of the roles where the skill is evidenced. Use `null`
  when the CV does not let you tell.
- **Recency matters**: record the last year each skill was used. A job search should
  lean on current skills.
- **Target roles**: 3 to 5 job titles the candidate could credibly apply for now, as
  they are written in job adverts, most fitting first.
- **Search keywords**: 5 to 10 short queries that combine a title with the strongest
  differentiating skills. These are what `job-finder` will search with.

## Output

Create `output/` if needed and write valid JSON to `output/skills.json`:

```json
{
  "source_cv": "profiles/<file>",
  "extracted_on": "YYYY-MM-DD",
  "candidate": {
    "name": "",
    "location": "",
    "current_title": "",
    "total_years_experience": 0,
    "seniority": "junior | mid | senior | lead | principal | manager",
    "summary": "two sentences describing the candidate as a recruiter would"
  },
  "skills": [
    {
      "name": "",
      "category": "language | framework | cloud | data | tool | practice | domain | soft",
      "level": "expert | proficient | familiar",
      "years": 0,
      "last_used": 2026,
      "evidence": "where in the CV this is shown"
    }
  ],
  "domains": ["industries or problem areas worked in"],
  "education": [{ "qualification": "", "institution": "", "year": 0 }],
  "certifications": [{ "name": "", "year": 0 }],
  "languages": [{ "language": "", "level": "" }],
  "target_roles": [""],
  "search_keywords": [""],
  "preferences": {
    "locations": [""],
    "work_mode": "remote | hybrid | onsite | any",
    "notes": "anything else from profiles/preferences.md"
  }
}
```

Sort `skills` by level (expert first), then by `last_used` (newest first). Use `null`
for facts the CV does not state; never guess a value.

Finish by replying with the candidate summary, the top ten skills, the target roles
and the path of the JSON file.
