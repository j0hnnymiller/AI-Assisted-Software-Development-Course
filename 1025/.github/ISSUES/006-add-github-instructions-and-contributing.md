Title: Add `.github/instructions` or CONTRIBUTING.md for discoverability

Problem

Contributor instructions are in `.vscode/instructions`, which is good for IDE-integrated guidance but not easily visible to new contributors browsing the repository or opening PRs.

Why it matters

- Contributors and maintainers expect developer-contribution guidance under `.github` or in `CONTRIBUTING.md` for visibility in PRs and issue templates.

Suggested fix

- Copy or move the current `.vscode/instructions` content into `.github/instructions/` or create a `CONTRIBUTING.md` with the key points (C# style, .NET SDK, WPF guidance, test guidance) and link to `.vscode/instructions` for detailed editor-integrated rules.

Acceptance criteria

- `.github/instructions/` or `CONTRIBUTING.md` exists and provides the same or better visibility for contribution guidelines.
- README links to CONTRIBUTING.md (optional but recommended).
