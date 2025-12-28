Title: Fix testing instructions: repo uses MSTest (not NUnit)

Problem

`.vscode/instructions/nunit.instructions.md` describes NUnit usage, but the actual test project (`calculator.tests`) uses MSTest (`MSTest.TestAdapter`, `MSTest.TestFramework`) and test files use `[TestClass]`/`[TestMethod]` attributes. This mismatch can confuse contributors and CI configuration.

Why it matters

- Contributors following the instructions may add NUnit-style tests that won't be discovered.
- CI configuration or docs referencing NUnit runners will be incorrect.

Suggested fix

- Rename or replace the instructions file to `mstest.instructions.md` and update the content to document using MSTest attributes, packages, and test discovery.
- Update `.vscode/instructions` and any README references to reflect MSTest.

Acceptance criteria

- Instruction file accurately documents MSTest usage and packages.
- No references to NUnit remain in the instructions.
