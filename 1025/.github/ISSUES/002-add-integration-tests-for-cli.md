Title: Add integration tests for CLI (Program.Main)

Description:
Create integration tests that run the calculator CLI end-to-end to exercise `Program.Main` and improve coverage of `Program.cs`.

Tasks:

- Add tests in `calculator.tests` that launch the calculator process via `dotnet run --project calculator -- "expression"` and capture stdout/stderr.
- Verify expected outputs for representative expressions and error cases (e.g., division by zero produces an error message and non-zero exit code).
- Ensure integration tests are stable and have reasonable timeouts.

Acceptance criteria:

- Integration tests added and passing on local runs.
- `Program.cs` coverage increases (aim to cover conditional branches in REPL and CLI code paths).

Notes:

- Use `ProcessStartInfo` with `UseShellExecute=false`, redirect stdout/stderr, and wait with a timeout.
- Keep tests small and focused to avoid long-running CI steps.
