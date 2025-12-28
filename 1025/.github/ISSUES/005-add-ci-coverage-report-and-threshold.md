Title: Add CI job to run tests, publish coverage, and enforce threshold

Description:
Add a GitHub Actions workflow to run tests, generate a coverage report (HTML), and fail the build if line coverage falls below 50%.

Tasks:

- Add `.github/workflows/ci.yml` that runs `dotnet test --collect:"XPlat Code Coverage"` and uses `reportgenerator` to create an HTML report.
- Upload coverage report as a workflow artifact and optionally publish to a coverage service.
- Add a step to parse Cobertura XML and fail the job if line-rate < 0.5.

Acceptance criteria:

- CI job runs tests and produces coverage artifacts.
- CI fails when coverage < 50%.

Notes:

- Use the `reportgenerator` tool for HTML report generation.
- Consider caching NuGet packages to speed up CI.
