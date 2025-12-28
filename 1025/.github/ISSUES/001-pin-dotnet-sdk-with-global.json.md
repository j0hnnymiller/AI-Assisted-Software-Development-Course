Title: Pin .NET SDK with global.json

Problem

The repository does not include a `global.json` file to pin the .NET SDK version. Without SDK pinning, developer machines and CI may pick different SDKs (for example, .NET 9 when available), which can lead to unexpected build or test failures.

Why it matters

- Reproducible builds: pinning ensures all contributors and CI use the same SDK.
- CI stability: prevents accidental breaks when a newer SDK changes behavior.

Suggested fix

Add a `global.json` at the repository root with a chosen SDK version, for example:

```json
{
  "sdk": {
    "version": "8.0.100"
  }
}
```

Notes

- Choose a specific SDK version that matches what your CI images provide, or update CI to install the pinned SDK.
- Document the pinned SDK in `README.md` or `SETUP.md`.

Acceptance criteria

- `global.json` exists at repo root.
- CI uses the pinned SDK and builds pass.
