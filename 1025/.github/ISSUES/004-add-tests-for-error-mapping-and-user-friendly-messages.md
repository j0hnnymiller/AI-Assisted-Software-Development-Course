Title: Add tests for error mapping and user-friendly messages

Description:
Improve error handling semantics and add tests that assert user-facing messages and error codes.

Tasks:

- Define a set of application-specific exception types (e.g., `CalculatorParseException`, `CalculatorEvaluationException`).
- Map low-level exceptions (OverflowException, ArgumentException, InvalidOperationException) to the application-specific types in `Evaluator`.
- Update `Program.cs` to print friendly messages based on these application exceptions.
- Add tests asserting that expected inputs produce the mapped exceptions and messages.

Acceptance criteria:

- Tests cover error mappings and messages.
- CLI prints user-friendly messages for error cases and exits with non-zero codes when appropriate.

Notes:

- Keep error messages concise and suitable for terminal display.
- This work helps with integration tests capturing `stderr` output.
