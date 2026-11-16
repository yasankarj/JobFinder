# API Documentation

## REST APIs
None.

## Internal APIs (slash commands)
### /cv-reviewer
- **Arguments**: `[path to CV] [target role]`
- **Reads**: the named CV, else the newest CV in `profiles/`; `config/preferences.md`
- **Writes**: `output/cv-review.md`

### /skill-extractor
- **Arguments**: `[path to CV]`
- **Reads**: the named CV, else the newest CV in `profiles/`; `config/preferences.md`
- **Writes**: `output/skills.json`

### /job-finder
- **Arguments**: `[extra criteria]` (no way to choose a profile)
- **Reads**: `output/skills.json`, `config/preferences.md`
- **Writes**: `output/jobs.md`

### /find-jobs
- **Arguments**: `[path to CV] [extra search criteria]`
- **Reads/Writes**: everything above

## Data Models
### output/skills.json (contract between skill-extractor and job-finder)
- **Fields**: `source_cv`, `extracted_on`, `candidate` {name, location, current_title,
  total_years_experience, seniority, summary}, `skills[]` {name, category, level, years,
  last_used, evidence}, `domains[]`, `education[]`, `certifications[]`, `languages[]`,
  `target_roles[]`, `search_keywords[]`, `preferences` {locations, work_mode, notes}
- **Relationships**: `source_cv` points back to one file in `profiles/`
- **Validation**: must parse as JSON (checked by find-jobs); unknown facts are `null`

### output/cv-review.md and output/jobs.md
- Markdown reports with fixed templates in their SKILL.md files; each names one
  candidate in its title.
