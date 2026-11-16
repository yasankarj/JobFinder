# Code Quality Assessment

## Test Coverage
- **Overall**: None
- **Unit Tests**: not applicable (instruction files)
- **Integration Tests**: none; verified by running the skills manually

## Code Quality Indicators
- **Linting**: Not configured
- **Code Style**: Consistent across the four SKILL.md files
- **Documentation**: Good (README, CLAUDE.md, templates inside each skill)

## Technical Debt
- Fixed output paths in all three worker skills mean only one candidate's results can
  exist at a time.
- `job-finder` cannot be pointed at a specific profile.
- Input selection rule ("most recently modified CV in `profiles/`") is duplicated in
  cv-reviewer and skill-extractor, and summarised again in find-jobs and README.
- Output paths are repeated in README.md and CLAUDE.md, so any path change touches docs too.

## Patterns and Anti-patterns
- **Good Patterns**: file hand-off so each step can re-run alone; evidence-only
  extraction; verified-only job listings; per-skill tool restrictions.
- **Anti-patterns**: implicit single-tenant assumption baked into file paths.
