Title: Update CI to use Windows runner for test job that runs `calculator.tests`

Problem

`calculator.tests` targets `net8.0-windows` and references the WPF project. These tests require a Windows environment. If CI uses Linux or macOS runners for the job, tests will fail.

Why it matters

- CI failures will happen intermittently if the workflow runs on the wrong OS image.
- PRs may incorrectly appear broken to reviewers.

Suggested fix

- Update the CI workflow to run the build/test job for `calculator.tests` on `windows-latest` (or a pinned Windows image).
- Ensure the runner has the .NET 8 SDK required (or install it during workflow) and cache NuGet packages.

Acceptance criteria

- CI job that builds/tests `calculator.tests` runs on a Windows runner and passes.
- Workflow documentation updated accordingly.
