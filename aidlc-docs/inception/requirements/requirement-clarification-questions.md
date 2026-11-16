# Requirements Clarification Questions

Your answers are clear on their own, but some of them interact. These five questions
settle those points before I write the requirements.

## Ambiguity 1: An individual with one candidate
You chose one layout for both advisors and individuals (Q1: A), and that every command
needs a candidate name (Q5: B). Together, that means an individual with only their own
CV would have to type their own folder name on every command.

### Clarification Question 1
When `profiles/` holds exactly one candidate and no name is given, what should happen?

A) Use that candidate automatically. A name is required only when there are two or more candidates.

B) Still require the name, so every command behaves the same way

X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Ambiguity 2: How several candidates get into one run
You chose to process candidates one after another (Q7: B), but also that a candidate
must always be named (Q5: B). It isn't clear whether one command can name several
candidates.

### Clarification Question 2
How can a user process more than one candidate?

A) A command takes one or more names, for example `/find-jobs jane-doe john-smith`, and runs them in that order

B) A command takes one or more names, or the word `all` for every candidate, and runs them in order

C) One candidate per command. To do several, run the command several times.

X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Ambiguity 3: The shared `config/preferences.md`
You chose per-candidate preferences only, with no shared defaults (Q4: B). That leaves
the current `config/preferences.md` with no role.

### Clarification Question 3
What should happen to `config/preferences.md`?

A) It becomes a blank template, for example `config/preferences.template.md`, that is copied into each new candidate's folder as `profiles/<candidate>/preferences.md`

B) Delete it. The candidate's `preferences.md` is written by hand, with the format documented in the README.

X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Ambiguity 4: The candidate's ID when migrating
You chose to move the current setup automatically (Q9: C). The current CV is called
`my-resume.pdf`, which makes a poor ID for a candidate.

### Clarification Question 4
How should the candidate folder name (the ID) be chosen when a loose CV is migrated?

A) From the candidate's name inside the CV, lower-case with hyphens, for example `profiles/jane-doe/`. Existing results in `output/` move to `output/jane-doe/`.

B) From the file name, for example `profiles/my-resume/`

C) Ask the user for the name before moving anything

X) Other (please describe after [Answer]: tag below)

[Answer]: A

## Missing: Adding a new candidate
None of the questions covered how a new candidate gets added. This matters most for
advisors, who will do it often.

### Clarification Question 5
How should a user add a new candidate?

A) A new command, for example `/add-candidate <path to CV> [name]`. It creates `profiles/<candidate>/`, copies the CV in and creates the preferences file.

B) By hand: create the folder and drop the CV in, as documented in the README. Loose CVs dropped into `profiles/` are migrated automatically, as in Q9.

X) Other (please describe after [Answer]: tag below)

[Answer]: B
