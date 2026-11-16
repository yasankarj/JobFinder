# Requirements: Multiple Candidates and CVs

## Intent Analysis
- **User request**: "All the skills are written to manager one CV at a time! I want the
  user to manager multiple CVs at a time!" Follow-up: "If we improve this enough, we can
  share it with recruitment advisors! If that is the case, the CVs cannot be only belongs
  to one person!"
- **Request type**: Enhancement (with a breaking change to file locations)
- **Scope**: Multiple components: all four product skills, `config/`, `README.md`, `CLAUDE.md`
- **Complexity**: Moderate
- **Depth**: Standard

## Decisions from Clarifying Questions
| Topic | Decision | Source |
|---|---|---|
| Users | Advisors and individuals share one layout; an individual is an advisor with one candidate | Q1 A |
| Input layout | One folder per candidate: `profiles/<candidate-id>/` | Q2 A |
| Output layout | Mirrors input: `output/<candidate-id>/cv-review.md`, `skills.json`, `jobs.md` | Q3 A |
| Preferences | Per candidate only: `profiles/<candidate-id>/preferences.md`; no shared defaults | Q4 B |
| Template | `config/preferences.md` becomes `config/preferences.template.md`, a blank template | C3 A |
| Selecting candidates | Name required when there are two or more candidates; used automatically when there is exactly one | Q5 B, C1 A |
| Several candidates per command | A command accepts one or more candidate IDs and runs them in the order given | C2 A |
| Several CVs for one candidate | Use the most recently modified CV in the candidate's folder | Q6 A |
| Execution | Sequential, one candidate after another | Q7 B |
| Cross-candidate overview | None | Q8 B |
| Existing single-CV setup | Migrated automatically | Q9 C |
| Candidate ID on migration | From the candidate's name inside the CV, lower-case with hyphens | C4 A |
| Adding a candidate | By hand: create the folder or drop a loose CV into `profiles/` | C5 B |

## Functional Requirements

### FR-1 Candidate model
- FR-1.1 A **candidate** is a folder `profiles/<candidate-id>/` holding one or more CV
  files (PDF, DOCX or Markdown) and an optional `preferences.md`.
- FR-1.2 A **candidate ID** is lower-case ASCII letters, digits and single hyphens,
  starting and ending with a letter or digit, at most 64 characters (for example
  `jane-doe`). Anything else is rejected.
- FR-1.3 The **active CV** of a candidate is the most recently modified CV file in its
  folder. A command may name a specific file in the folder to override this.

### FR-2 Selecting candidates
- FR-2.1 Every command (`/cv-reviewer`, `/skill-extractor`, `/job-finder`, `/find-jobs`)
  accepts zero or more candidate IDs as its leading arguments.
- FR-2.2 Arguments that match an existing candidate ID are candidates; the remaining
  arguments keep their current meaning (target role for `cv-reviewer`, search criteria
  for `job-finder` and `find-jobs`).
- FR-2.3 With no candidate named: if exactly one candidate exists, use it and say so;
  if two or more exist, stop and list the candidate IDs; if none exist, stop and explain
  how to add one.
- FR-2.4 A named ID that does not exist stops the command before any work, listing the
  valid IDs.
- FR-2.5 Several named candidates are processed one after another, in the order given.
  A failure for one candidate is reported and does not stop the others.

### FR-3 Outputs per candidate
- FR-3.1 Each skill writes to `output/<candidate-id>/` with the current file names:
  `cv-review.md`, `skills.json`, `jobs.md`.
- FR-3.2 `job-finder` reads `output/<candidate-id>/skills.json`. If it is missing, it
  tells the user to run `/skill-extractor <candidate-id>` first.
- FR-3.3 `skills.json` keeps its current schema, plus a `candidate_id` field. `source_cv`
  holds the path of the CV it was extracted from.
- FR-3.4 A run for one candidate never reads or writes another candidate's files.

### FR-4 Preferences per candidate
- FR-4.1 Each skill reads `profiles/<candidate-id>/preferences.md` for that candidate
  only. There is no shared fallback.
- FR-4.2 `config/preferences.md` is renamed to `config/preferences.template.md` and kept
  blank (versioned).
- FR-4.3 When a command finds a candidate without `preferences.md`, it copies the
  template into the candidate's folder, tells the user once, and carries on with blank
  preferences.

### FR-5 Adding candidates and migration
- FR-5.1 Users add a candidate by hand: create `profiles/<candidate-id>/` and put a CV in
  it, or drop a CV loose into `profiles/`.
- FR-5.2 Before selecting candidates, every command migrates **loose CVs** (CV files
  directly in `profiles/`):
  1. Read the candidate's name from the CV and turn it into an ID (lower-case, hyphens,
     accents removed, other characters dropped).
  2. If no name can be read, fall back to the file name made into a valid ID, and say so.
  3. Move the CV into `profiles/<candidate-id>/`, keeping its file name.
  4. Copy the preferences template in (FR-4.3).
- FR-5.3 **Legacy results**: if `output/` holds `cv-review.md`, `skills.json` or
  `jobs.md` directly, move them to `output/<candidate-id>/` for the candidate whose CV
  `skills.json` `source_cv` names (or the CV just migrated, when there was only one).
  Update `source_cv` to the new path and add `candidate_id`.
- FR-5.4 **Name collision**: if `profiles/<candidate-id>/` already exists, the loose CV
  is added to it only when it is clearly the same person (same name and at least one
  matching email or phone number). Otherwise use the next free ID with a numeric suffix
  (`jane-doe-2`) and tell the user.
- FR-5.5 Migration never overwrites a file. If a destination file already exists, leave
  the source where it is and report it.
- FR-5.6 After migrating, the command reports what moved where.

### FR-6 Pipeline
- FR-6.1 `/find-jobs [candidate-id ...] [criteria]` runs review, extraction and search
  for each candidate in turn, then gives one summary per candidate.
- FR-6.2 Existing per-step checks (JSON parses, failures reported as failures) apply per
  candidate.

### FR-7 Documentation
- FR-7.1 `README.md` and `CLAUDE.md` describe the candidate layout, how to add a
  candidate, selection rules, the new output paths and the preferences template.

## Non-Functional Requirements

### NFR-1 Privacy and isolation
- NFR-1.1 `profiles/` and `output/` stay git-ignored; per-candidate preferences live in
  `profiles/`, so third-party data is never committed.
- NFR-1.2 Web searches use skills, titles, locations and keywords only. A candidate's
  name, contact details or employer-identifying details never appear in a query.
- NFR-1.3 Candidate names and CV content never appear in `aidlc-docs/` artifacts.
- NFR-1.4 Each candidate's reports mention only that candidate.

### NFR-2 Safety
- NFR-2.1 Candidate IDs are validated (FR-1.2) before any path is built from them, so an
  argument cannot reach outside `profiles/<id>/` or `output/<id>/` (for example `..` or `/`).
- NFR-2.2 Fail closed: on an invalid ID, an ambiguous selection or a migration conflict,
  stop or skip that item. Never guess, delete or overwrite.

### NFR-3 Compatibility
- NFR-3.1 This is a breaking change to output paths (the `skills.json` location moves),
  so the commit is marked `feat!`.
- NFR-3.2 After migration, the current single-CV user keeps working with no arguments
  (FR-2.3, exactly one candidate).

### NFR-4 Maintainability
- NFR-4.1 The rules for selecting candidates, migrating and validating IDs are written
  once and shared by the skills rather than copied into four places, if Claude Code skills
  allow it. Application Design decides how.

## Out of Scope
- An overview across candidates (Q8 B)
- Running candidates in parallel (Q7 B)
- An `/add-candidate` command (C5 B)
- Merging several CVs into one profile (Q6 A)

## Extension Compliance (Requirements Analysis)
### Security Baseline
| Rule | Status | Note |
|---|---|---|
| SECURITY-01 Encryption at rest/in transit | N/A | Local files only; no data store is provisioned |
| SECURITY-02 Access logging on intermediaries | N/A | No network intermediaries |
| SECURITY-03 Application logging | Compliant | No logging infrastructure; NFR-1.3 keeps PII out of the audit trail |
| SECURITY-04 HTTP headers | N/A | No web endpoints |
| SECURITY-05 Input validation | Compliant | FR-1.2, NFR-2.1 validate candidate IDs before path use |
| SECURITY-06 Least privilege | Compliant | Skills keep their `allowed-tools` restrictions; any new tool must be justified in design |
| SECURITY-07 Network configuration | N/A | No network configuration |
| SECURITY-08 Application access control | Compliant | No authentication; object-level isolation required by FR-3.4 |
| SECURITY-09 Hardening | N/A | Nothing is deployed |
| SECURITY-10 Supply chain | N/A | No dependencies |
| SECURITY-11 Secure design | Compliant | Misuse cases covered: path traversal (NFR-2.1), name collision (FR-5.4), data leaking into searches (NFR-1.2) |
| SECURITY-12 Authentication | N/A | No authentication |
| SECURITY-13 Data integrity | Compliant | Migration never overwrites and reports every move (FR-5.5, FR-5.6) |
| SECURITY-14 Alerting | N/A | No deployed service |
| SECURITY-15 Fail-safe defaults | Compliant | NFR-2.2 |

### Property-Based Testing (Partial)
PBT-02, 03, 07, 08 and 09 are blocking, but they apply only to executable code. The
product is Markdown instructions today. If Application Design adds a script (for example
for ID validation or migration), these rules apply to it; otherwise they are N/A.
Decided at Workflow Planning.
