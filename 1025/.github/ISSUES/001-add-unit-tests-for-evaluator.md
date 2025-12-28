Title: Add unit tests for evaluator arithmetic and edge cases

Description:
Add a comprehensive suite of unit tests for `CalculatorLib.Evaluator` that bring line coverage closer to 50%. Focus areas:

- Additional arithmetic cases (negative numbers, modulo, mixed precedence)
- Floating point precision checks and tolerances
- Edge cases already partially covered (division by zero, large number overflow)
- Error cases: malformed expressions, unexpected tokens

Acceptance criteria:

- Unit tests added under `calculator.tests` and passing.
- Coverage increase of at least 10 percentage points for the evaluator file.
- Tests are idempotent (run reliably on CI).

Notes:

- Use `Assert.ThrowsException<T>` for expected exceptions.
- Prefer deterministic inputs; avoid randomness or time-based logic.
