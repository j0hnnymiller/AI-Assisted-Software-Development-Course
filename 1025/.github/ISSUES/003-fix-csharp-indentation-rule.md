Title: Fix C# instruction indentation rule to match repo style

Problem

`.vscode/instructions/csharp.instructions.md` currently states "Use 2 spaces for indentation." The codebase (e.g., `Program.cs`, `Evaluator.cs`) uses 4-space indentation for C# files.

Why it matters

- Mismatched style guidance leads to inconsistent formatting in PRs and unnecessary churn from formatters.

Suggested fix

- Update `csharp.instructions.md` to require 4-space indentation (or instruct contributors to follow existing repository formatting and run dotnet-format if added).

Acceptance criteria

- `csharp.instructions.md` reflects 4-space indentation.
- Optionally add a `dotnet format` or editorconfig configuration to enforce style.
