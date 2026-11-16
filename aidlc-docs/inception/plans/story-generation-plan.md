# Story Generation Plan

Source: `aidlc-docs/inception/requirements/requirements.md`

## Breakdown Approaches Considered
| Approach | Fit for this change |
|---|---|
| User journey-based | Good: follows "add candidate, run, read results", which is how people use JobFinder |
| Feature-based | Good: maps cleanly to FR-1 to FR-7 and to the skills that change |
| Persona-based | Weak alone: most stories serve both personas |
| Domain-based | Not useful: one small domain |
| Epic-based | Overkill for roughly 10 to 15 stories |

## Questions

Fill in each `[Answer]:` with a letter, or choose Other and describe what you want.

## Question 1
Which personas should the stories cover?

A) Two: the recruitment advisor (many candidates) and the individual job seeker (one candidate, themselves)

B) Three: as A, plus the candidate whose CV an advisor holds (a passive persona whose privacy matters)

X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 2
How should the stories be organised?

A) By user journey: set up candidates, run commands, read results, migrate the existing setup

B) By feature, following the requirement groups FR-1 to FR-7

X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 3
What format should the acceptance criteria use?

A) Given / When / Then scenarios, so each one reads as a manual test step

B) A checklist of plain statements per story

X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 4
Should edge cases and misuse cases have their own stories, or sit inside the main stories as acceptance criteria? Examples: an invalid candidate ID, two people with the same name, a missing `skills.json`.

A) Inside the main stories as acceptance criteria, which keeps the count small

B) As separate stories, which makes them more visible when testing

X) Other (please describe after [Answer]: tag below)

[Answer]: B

## Approach Decided
- **Personas**: Recruitment advisor and individual job seeker (Q1 A)
- **Organisation**: By feature, one section per requirement group FR-1 to FR-7 (Q2 B)
- **Acceptance criteria**: Checklist of plain, testable statements (Q3 B)
- **Edge and misuse cases**: Separate stories, placed in the feature section they belong to and marked "Edge case" (Q4 B)

## Execution Checklist (Part 2, after approval)
- [x] Generate `aidlc-docs/inception/user-stories/personas.md` with user archetypes and characteristics
- [x] Generate `aidlc-docs/inception/user-stories/stories.md` organised by the chosen approach
- [x] Ensure stories meet INVEST (Independent, Negotiable, Valuable, Estimable, Small, Testable)
- [x] Write acceptance criteria for each story in the chosen format
- [x] Map personas to stories
- [x] Trace each story to its requirement IDs (FR / NFR)
- [x] Check every functional requirement is covered by at least one story
- [x] Check stories contain no real candidate names or CV content (NFR-1.3)
