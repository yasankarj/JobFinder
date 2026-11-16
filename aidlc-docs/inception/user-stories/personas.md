# Personas

## P1 Recruitment Advisor
- **Who**: Places candidates into roles for a living; keeps CVs for many candidates at once.
- **Goals**: Review CVs, build profiles and find matching jobs for several candidates
  without results getting mixed up; add new candidates quickly.
- **Typical volume**: 5 to 50 candidates, each with one or more CV versions.
- **Frustrations**: Results overwritten by the next candidate's run; having to reshuffle
  files; one candidate's details showing up in another's report.
- **Responsibilities**: Holds third-party personal data, so needs isolation between
  candidates and confidence that nothing leaks into web searches or version control.
- **Technical comfort**: Comfortable typing slash commands and dropping files into
  folders; not interested in editing JSON.

## P2 Individual Job Seeker
- **Who**: Looking for their own next role; the only candidate in their `profiles/`.
- **Goals**: Run `/find-jobs` with no arguments and get a review and shortlist, as today.
- **Typical volume**: 1 candidate, possibly with a few CV versions (for example backend
  and data).
- **Frustrations**: Extra setup steps or arguments that only make sense for advisors;
  losing earlier results when the layout changes.
- **Technical comfort**: Comfortable with Claude Code; expects sensible defaults.

## Persona to Story Map
| Story | P1 Advisor | P2 Individual |
|---|---|---|
| US-1.1 Candidate folder | Yes | Yes |
| US-1.2 Invalid ID rejected (Edge case) | Yes | Yes |
| US-2.1 No arguments with one candidate | | Yes |
| US-2.2 Run for a named candidate | Yes | |
| US-2.3 Run for several candidates | Yes | |
| US-2.4 No name, several candidates (Edge case) | Yes | |
| US-2.5 Unknown or no candidates (Edge case) | Yes | Yes |
| US-2.6 One candidate fails in a batch (Edge case) | Yes | |
| US-3.1 Results per candidate | Yes | Yes |
| US-3.2 Job search uses the named profile | Yes | Yes |
| US-3.3 Missing profile (Edge case) | Yes | Yes |
| US-3.4 No cross-candidate access (Edge case) | Yes | |
| US-3.5 No personal data in web searches (Edge case) | Yes | Yes |
| US-4.1 Preferences per candidate | Yes | Yes |
| US-4.2 Template copied when missing | Yes | Yes |
| US-5.1 Existing setup migrates | | Yes |
| US-5.2 Add a candidate by dropping a CV | Yes | |
| US-5.3 Add a candidate by creating a folder | Yes | |
| US-5.4 No readable name (Edge case) | Yes | Yes |
| US-5.5 Same name, different person (Edge case) | Yes | |
| US-5.6 Same person, another CV (Edge case) | Yes | Yes |
| US-5.7 Destination file exists (Edge case) | Yes | Yes |
| US-6.1 Full pipeline per candidate | Yes | Yes |
| US-6.2 Pipeline step fails for one candidate (Edge case) | Yes | |
| US-7.1 Documentation | Yes | Yes |
