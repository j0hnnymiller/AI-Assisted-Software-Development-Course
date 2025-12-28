Title: Refactor evaluator to separate parser and executor

Description:
Refactor `CalculatorLib.Evaluator` by extracting parsing logic from evaluation/execution. This will make the parser easier to unit test and allow targeted tests for parsing, operator precedence, and error handling.

Tasks:

- Create a parser module that converts expression strings into an AST or RPN (Shunting Yard output).
- Create an executor that evaluates AST/RPN to numeric results.
- Keep current behavior and public API stable (Evaluator.Evaluate) for callers while adding internal types for testing.

Acceptance criteria:

- Parser code is covered by unit tests (syntax tree correctness and error recovery).
- Evaluator tests continue to pass.
- Easier place to add tests for functions (sqrt, pow) later.

Notes:

- Consider public/internal visibility and use `InternalsVisibleTo` if needed for tests.
- This is a medium-risk refactor; add tests before changing behavior.
