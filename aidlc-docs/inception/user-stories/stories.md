# User Stories: Multiple Candidates and CVs

Organised by requirement group (FR-1 to FR-7). Edge and misuse cases are separate
stories marked **Edge case**. Personas: **P1** Recruitment Advisor, **P2** Individual
Job Seeker (see `personas.md`). Example IDs such as `jane-doe` are fictional.

---

## FR-1 Candidate Model

### US-1.1 Keep each candidate's CVs in their own folder
**As** an advisor (P1) or individual (P2), **I want** each candidate's CVs in a folder of
their own, **so that** files for different people never mix.
- **Traces to**: FR-1.1, FR-1.3
- **Acceptance criteria**:
  - [ ] A folder `profiles/<candidate-id>/` with one or more CVs (PDF, DOCX or Markdown) is recognised as a candidate.
  - [ ] When the folder holds several CVs, the most recently modified one is used, and the command says which.
  - [ ] Naming a specific CV file in the candidate's folder uses that file instead.
  - [ ] Files in the folder that are not CVs (such as `preferences.md`) are never treated as CVs.

### US-1.2 Edge case: an invalid candidate ID is rejected
**As** an advisor (P1), **I want** a malformed candidate ID to be refused, **so that** a
typo or a crafted argument cannot read or write outside the candidate's folders.
- **Traces to**: FR-1.2, NFR-2.1, NFR-2.2
- **Acceptance criteria**:
  - [ ] IDs with upper-case letters, spaces, `/`, `\`, `.` or `..`, a leading or trailing hyphen, or a double hyphen are rejected.
  - [ ] An ID longer than 64 characters is rejected.
  - [ ] Rejection happens before any file is read or written, and the message says what a valid ID looks like.

---

## FR-2 Selecting Candidates

### US-2.1 Run with no arguments when there is one candidate
**As** an individual (P2), **I want** commands to work with no arguments, **so that**
JobFinder is as easy to use as before.
- **Traces to**: FR-2.3, NFR-3.2
- **Acceptance criteria**:
  - [ ] With exactly one candidate in `profiles/`, `/find-jobs`, `/cv-reviewer`, `/skill-extractor` and `/job-finder` run for that candidate with no arguments.
  - [ ] The command states which candidate it chose.

### US-2.2 Run a command for a named candidate
**As** an advisor (P1), **I want** to name the candidate a command runs for, **so that**
I control whose CV is processed.
- **Traces to**: FR-2.1, FR-2.2
- **Acceptance criteria**:
  - [ ] `/cv-reviewer jane-doe "Data Engineer"` reviews only jane-doe's CV, against the Data Engineer role.
  - [ ] `/job-finder jane-doe remote only` searches for jane-doe with "remote only" as extra criteria.
  - [ ] Arguments that are not an existing candidate ID keep their current meaning.

### US-2.3 Run a command for several candidates
**As** an advisor (P1), **I want** to name several candidates in one command, **so that**
I can process a batch without retyping the command.
- **Traces to**: FR-2.5
- **Acceptance criteria**:
  - [ ] `/skill-extractor jane-doe john-smith` processes jane-doe, then john-smith, in that order.
  - [ ] Each candidate's results go to their own output folder.
  - [ ] The final reply reports the result for each candidate separately.

### US-2.4 Edge case: no name given when there are several candidates
**As** an advisor (P1), **I want** the command to stop and list my candidates when I
forget to name one, **so that** it never guesses whose data to process.
- **Traces to**: FR-2.3, NFR-2.2
- **Acceptance criteria**:
  - [ ] With two or more candidates and no ID given, the command does no work.
  - [ ] It lists the available candidate IDs and shows an example command.

### US-2.5 Edge case: an unknown candidate, or no candidates at all
**As** a user (P1 or P2), **I want** a clear message when the candidate I name doesn't
exist or there are none, **so that** I know how to fix it.
- **Traces to**: FR-2.3, FR-2.4
- **Acceptance criteria**:
  - [ ] An ID that is valid but has no folder stops the command before any work, and lists the valid IDs.
  - [ ] When several IDs are given and one is unknown, nothing runs, so a batch is never half-done because of a typo.
  - [ ] With no candidates and no loose CVs in `profiles/`, the command explains how to add one.

### US-2.6 Edge case: one candidate fails during a batch
**As** an advisor (P1), **I want** one candidate's failure not to stop the rest, **so
that** a single unreadable CV doesn't waste the whole run.
- **Traces to**: FR-2.5
- **Acceptance criteria**:
  - [ ] If processing fails for one candidate (for example the CV cannot be read), the remaining candidates are still processed.
  - [ ] The final reply lists the failed candidate, with the reason, separately from the successful ones.
  - [ ] No partial output from the failed candidate is presented as a result.

---

## FR-3 Outputs per Candidate

### US-3.1 Results are stored per candidate
**As** an advisor (P1) or individual (P2), **I want** each candidate's results kept
separately, **so that** a run for one candidate never overwrites another's.
- **Traces to**: FR-3.1, FR-3.3
- **Acceptance criteria**:
  - [ ] `cv-reviewer`, `skill-extractor` and `job-finder` write to `output/<candidate-id>/cv-review.md`, `skills.json` and `jobs.md`.
  - [ ] `skills.json` contains `candidate_id` matching the folder name, and `source_cv` naming the CV used.
  - [ ] Running a skill for candidate B leaves candidate A's output files unchanged.

### US-3.2 Job search uses the named candidate's profile
**As** a user (P1 or P2), **I want** `/job-finder` to search with the right candidate's
profile, **so that** the shortlist matches that person.
- **Traces to**: FR-3.2
- **Acceptance criteria**:
  - [ ] `/job-finder jane-doe` reads `output/jane-doe/skills.json` and no other profile.
  - [ ] The report title and summary name only that candidate.

### US-3.3 Edge case: the candidate has no profile yet
**As** a user (P1 or P2), **I want** a clear next step when I search before extracting
skills, **so that** I don't get a search built on nothing.
- **Traces to**: FR-3.2
- **Acceptance criteria**:
  - [ ] If `output/<candidate-id>/skills.json` is missing, `job-finder` stops for that candidate and says to run `/skill-extractor <candidate-id>`.
  - [ ] It never falls back to another candidate's profile or to the old flat `output/skills.json`.

### US-3.4 Edge case: a run never touches another candidate's files
**As** an advisor (P1), **I want** each run confined to its candidate, **so that** one
person's data never appears in another's report.
- **Traces to**: FR-3.4, NFR-1.4
- **Acceptance criteria**:
  - [ ] A run for jane-doe reads only `profiles/jane-doe/` and `output/jane-doe/` (plus the shared template and migration of loose files).
  - [ ] Reports name only their own candidate.

### US-3.5 Edge case: web searches carry no personal data
**As** an advisor (P1) holding other people's CVs, **I want** searches to contain only
job-related terms, **so that** candidates' personal details are not sent to search engines.
- **Traces to**: NFR-1.2
- **Acceptance criteria**:
  - [ ] Search queries contain only titles, skills, locations, work mode and keywords.
  - [ ] A candidate's name, email, phone number, address or current employer never appears in a query.
  - [ ] The "Search notes" section of `jobs.md` lists the queries used, so this can be checked.

---

## FR-4 Preferences per Candidate

### US-4.1 Each candidate has their own preferences
**As** an advisor (P1), **I want** each candidate to have their own location, salary and
work-mode preferences, **so that** each search fits that person.
- **Traces to**: FR-4.1
- **Acceptance criteria**:
  - [ ] Skills read `profiles/<candidate-id>/preferences.md` for that candidate only.
  - [ ] Preferences for one candidate never affect another's search.
  - [ ] There is no fallback to a shared preferences file.

### US-4.2 A blank preferences file is created when missing
**As** a user (P1 or P2), **I want** a blank preferences file to appear in a candidate's
folder, **so that** I can see what I'm able to fill in.
- **Traces to**: FR-4.2, FR-4.3
- **Acceptance criteria**:
  - [ ] `config/preferences.template.md` exists, is blank, and replaces `config/preferences.md`.
  - [ ] When a command runs for a candidate with no `preferences.md`, the template is copied in, and the user is told once.
  - [ ] An existing `preferences.md` is never overwritten.
  - [ ] The run continues with blank preferences, inferring from the CV as before.

---

## FR-5 Adding Candidates and Migration

### US-5.1 My existing single-CV setup migrates automatically
**As** an individual (P2), **I want** my current CV and results moved into the new
layout for me, **so that** I keep my results and don't have to learn the new layout first.
- **Traces to**: FR-5.2, FR-5.3, FR-5.6, NFR-3.2
- **Acceptance criteria**:
  - [ ] On the first command after upgrading, a loose `profiles/<file>` is moved to `profiles/<id>/<file>`, where `<id>` comes from the candidate's name in the CV.
  - [ ] Loose `output/cv-review.md`, `skills.json` and `jobs.md` move to `output/<id>/`.
  - [ ] The moved `skills.json` gets `candidate_id`, and its `source_cv` points to the new CV path.
  - [ ] The command reports each move (from, to), then carries on with the requested work.
  - [ ] Running the command again moves nothing more.

### US-5.2 Add a candidate by dropping a CV into `profiles/`
**As** an advisor (P1), **I want** to add a candidate by saving their CV into
`profiles/`, **so that** onboarding takes one step.
- **Traces to**: FR-5.1, FR-5.2
- **Acceptance criteria**:
  - [ ] A CV saved loose in `profiles/` is moved into a new candidate folder on the next command.
  - [ ] A preferences file is created in the new folder (US-4.2).
  - [ ] The user is told the new candidate ID.

### US-5.3 Add a candidate by creating a folder
**As** an advisor (P1), **I want** to choose a candidate's ID myself by creating the
folder, **so that** IDs follow my own naming.
- **Traces to**: FR-5.1
- **Acceptance criteria**:
  - [ ] A folder `profiles/<valid-id>/` containing a CV is a candidate, with no migration needed.
  - [ ] A folder with an invalid name is reported and ignored.
  - [ ] A folder with no CV is reported when it is named in a command.

### US-5.4 Edge case: no name can be read from the CV
**As** a user (P1 or P2), **I want** a CV without a readable name to be migrated safely,
**so that** it isn't lost or left loose forever.
- **Traces to**: FR-5.2
- **Acceptance criteria**:
  - [ ] The ID falls back to the file name, made into a valid ID (for example `My Resume (2).pdf` becomes `my-resume-2`).
  - [ ] The user is told the fallback was used and can rename the folder.

### US-5.5 Edge case: two different people with the same name
**As** an advisor (P1), **I want** two different candidates with the same name kept
apart, **so that** one person's CV doesn't join another's folder.
- **Traces to**: FR-5.4, NFR-2.2
- **Acceptance criteria**:
  - [ ] If the target folder exists and the CVs share no email or phone number, the new CV goes to the next free suffix (`jane-doe-2`, `jane-doe-3`, ...).
  - [ ] The user is told about the suffix and why it was added.

### US-5.6 Edge case: a second CV for the same person
**As** a user (P1 or P2), **I want** a new CV version of an existing candidate to join
their folder, **so that** the candidate's newest CV is used.
- **Traces to**: FR-5.4, FR-1.3
- **Acceptance criteria**:
  - [ ] A loose CV with the same name and at least one matching email or phone number moves into the existing folder.
  - [ ] It becomes the active CV if it is the most recently modified.

### US-5.7 Edge case: the destination file already exists
**As** a user (P1 or P2), **I want** migration never to overwrite a file, **so that** no
CV or result is lost.
- **Traces to**: FR-5.5, NFR-2.2
- **Acceptance criteria**:
  - [ ] If a file with the same name already exists at the destination, the source stays where it is.
  - [ ] The conflict is reported with both paths.
  - [ ] No file is deleted by migration.

---

## FR-6 Pipeline

### US-6.1 Run the full pipeline for one or more candidates
**As** a user (P1 or P2), **I want** `/find-jobs` to run review, extraction and search for
each candidate I name, **so that** I get every result in one command.
- **Traces to**: FR-6.1, FR-6.2
- **Acceptance criteria**:
  - [ ] `/find-jobs jane-doe john-smith` runs all three steps for jane-doe, then all three for john-smith.
  - [ ] The final reply gives one summary per candidate, in the current summary shape.
  - [ ] Search criteria after the IDs apply to every candidate in the run.

### US-6.2 Edge case: a pipeline step fails for one candidate
**As** an advisor (P1), **I want** a failed step to stop only that candidate's pipeline,
**so that** the others still get results.
- **Traces to**: FR-6.2, FR-2.5
- **Acceptance criteria**:
  - [ ] If `skills.json` for a candidate doesn't parse after one retry, `job-finder` is skipped for that candidate and the failure is reported.
  - [ ] The remaining candidates continue.
  - [ ] Failed steps are reported as failed, never filled in from memory.

---

## FR-7 Documentation

### US-7.1 The docs explain the candidate layout
**As** a new user (P1 or P2), **I want** the README to explain candidates, **so that** I
can set up and run JobFinder without reading the skills.
- **Traces to**: FR-7.1
- **Acceptance criteria**:
  - [ ] `README.md` shows the `profiles/<id>/` and `output/<id>/` layout and how to add a candidate.
  - [ ] It explains the selection rules (one candidate: automatic; several: name them) with examples.
  - [ ] It explains the preferences template and per-candidate preferences.
  - [ ] `CLAUDE.md` lists the new paths and the `skills.json` contract location.

---

## Requirement Coverage
| Requirement | Stories |
|---|---|
| FR-1.1 | US-1.1 |
| FR-1.2 | US-1.2 |
| FR-1.3 | US-1.1, US-5.6 |
| FR-2.1, FR-2.2 | US-2.2 |
| FR-2.3 | US-2.1, US-2.4, US-2.5 |
| FR-2.4 | US-2.5 |
| FR-2.5 | US-2.3, US-2.6, US-6.2 |
| FR-3.1, FR-3.3 | US-3.1 |
| FR-3.2 | US-3.2, US-3.3 |
| FR-3.4 | US-3.4 |
| FR-4.1 | US-4.1 |
| FR-4.2, FR-4.3 | US-4.2 |
| FR-5.1 | US-5.2, US-5.3 |
| FR-5.2 | US-5.1, US-5.2, US-5.4 |
| FR-5.3 | US-5.1 |
| FR-5.4 | US-5.5, US-5.6 |
| FR-5.5 | US-5.7 |
| FR-5.6 | US-5.1 |
| FR-6.1, FR-6.2 | US-6.1, US-6.2 |
| FR-7.1 | US-7.1 |
| NFR-1.2 | US-3.5 |
| NFR-1.4 | US-3.4 |
| NFR-2.1, NFR-2.2 | US-1.2, US-2.4, US-5.5, US-5.7 |
| NFR-3.2 | US-2.1, US-5.1 |
