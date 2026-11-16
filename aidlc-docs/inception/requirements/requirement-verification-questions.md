# Requirements Clarification Questions: Multiple CVs

Request: let the user manage multiple CVs at a time. The CVs may belong to different
people, because recruitment advisors could use JobFinder for their candidates.

Answer each question by writing the letter after the `[Answer]:` tag. If no option
fits, choose the last option (Other) and describe what you want.

## Question 1
Who should JobFinder serve?

A) Recruitment advisors managing many candidates, and individuals, with the same layout (an individual is an advisor with one candidate)

B) Recruitment advisors only

C) Individuals only, with several CV versions for themselves

X) Other (please describe after [Answer]: tag below)

[Answer]:  A

## Question 2
How should CVs be organised in `profiles/`?

A) One folder per candidate, for example `profiles/jane-doe/cv.pdf`. The folder name is the candidate's ID.

B) A flat folder. The candidate is worked out from the file name, for example `profiles/jane-doe.pdf`.

C) A flat folder. The candidate is worked out from the name inside each CV.

X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 3
Where should results go?

A) One folder per candidate that mirrors `profiles/`, for example `output/jane-doe/cv-review.md`, `skills.json` and `jobs.md`

B) A flat `output/` folder with the candidate in each file name, for example `output/jane-doe-skills.json`

X) Other (please describe after [Answer]: tag below)

[Answer]:  A

## Question 4
How should search preferences work when each candidate wants different things (location, salary, work mode)?

A) `config/preferences.md` holds shared defaults, and an optional `preferences.md` in a candidate's folder overrides them. The per-candidate file sits in the git-ignored `profiles/`, so third-party data is never committed.

B) Per-candidate preferences only, with no shared defaults

C) Shared preferences only, as today

X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 5
What should a command do when you don't say which candidate?

A) Run for every candidate in `profiles/`. Arguments can name one or more candidates to narrow it down.

B) Always require a candidate name. Stop and list the candidates if none is given.

C) Run for every candidate who has not been processed yet, or whose CV changed since the last run

X) Other (please describe after [Answer]: tag below)

[Answer]:  B

## Question 6
What should happen when one candidate has several CV files (for example a backend CV and a data CV)?

A) Use the most recently modified CV in that candidate's folder, as today

B) Treat each CV file as its own profile with its own results, for example `output/jane-doe/backend/`

C) Merge all of the candidate's CVs into one profile, with evidence from each

X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 7
When several candidates are processed in one run, how should they run?

A) In parallel, one forked agent per candidate per step. This is faster, but uses more tokens at once and runs more web searches at the same time.

B) One candidate after another

X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 8
Should there be an overview across all candidates?

A) Yes: `/find-jobs` writes `output/index.md`, a table of each candidate with their CV score, seniority, top matches and the paths to their reports.

B) No, only per-candidate reports

X) Other (please describe after [Answer]: tag below)

[Answer]:  B

## Question 9
What should happen to the current single-CV setup (`profiles/my-resume.pdf` and the results in `output/`)?

A) Keep it working: a CV loose in `profiles/` is treated as its own candidate, named after the file. Nothing has to be moved.

B) Require the new layout. Commands stop and tell the user how to move a loose CV into a candidate folder.

C) Move it automatically into the new layout the first time a command runs

X) Other (please describe after [Answer]: tag below)

[Answer]: C

## Question: Security Extensions
Should security extension rules be enforced for this project?

A) Yes: enforce all SECURITY rules as blocking constraints (recommended for production-grade applications)

B) No: skip all SECURITY rules (suitable for PoCs, prototypes, and experimental projects)

X) Other (please describe after [Answer]: tag below)

[Answer]:  A

## Question: Property-Based Testing Extension
Should property-based testing (PBT) rules be enforced for this project?

A) Yes: enforce all PBT rules as blocking constraints (recommended for projects with business logic, data transformations, serialization, or stateful components)

B) Partial: enforce PBT rules only for pure functions and serialization round-trips (suitable for projects with limited algorithmic complexity)

C) No: skip all PBT rules (suitable for simple CRUD applications, UI-only projects, or thin integration layers with no significant business logic)

X) Other (please describe after [Answer]: tag below)

[Answer]:  B

## Question: Resiliency Extensions
Should the resiliency baseline be applied to this project?

This extension applies design-time best practices for fault tolerance, availability, observability and recovery, taken from the AWS Well-Architected Framework (Reliability Pillar). It is a starting point, not a certification that the workload is production-ready.

A) Yes: apply the resiliency baseline as directional best practices and design-time guidance (recommended for business-critical workloads)

B) No: skip the resiliency baseline (suitable for PoCs, prototypes, and experimental projects where rapid iteration matters more than reliability)

X) Other (please describe after [Answer]: tag below)

[Answer]: B
