# Instruction File Authoring Guide

## Introduction

This guide establishes standards for creating instruction files in this repository. Instruction files provide detailed, actionable guidance for contributors on specific tasks, processes, or workflows. Use this guide when you need to document a repeatable process, establish a standard procedure, or provide step-by-step instructions for repository contributors.

Instruction files help maintain consistency, reduce ambiguity, and ensure that critical processes are well-documented and accessible to all team members.

## Audience and Scope

- **Audience**: Contributors authoring instruction files for this repository
- **Scope**: Defines standard sections, directory placement, naming conventions, a reusable template, examples, and a quality checklist for instruction files
- **Out of scope**: General documentation guidelines, code comments, API documentation, or user-facing help content

## Prerequisites

- Basic Markdown knowledge
- Write access to the repository
- Understanding of the topic you're documenting
- Familiarity with the repository structure and conventions

## Output Requirements

- **File location**: `.github/instructions/`
- **Filename format**: `<topic-name>.instruction.md` (hyphenated lowercase)
- **Format**: Valid Markdown that renders correctly in GitHub and VS Code preview
- **Self-contained**: No external dependencies for core content
- **No wrapper text**: Instruction files should contain only the instruction content, not meta-commentary about the file itself

## File Placement and Naming Conventions

### Directory Structure

All instruction files must be placed in:

```
.github/instructions/
```

### Naming Rules

1. Use hyphenated lowercase only (kebab-case)
2. Must end with `.instruction.md`
3. Be descriptive but concise
4. One instruction file per topic
5. Avoid version numbers in filenames (use frontmatter for versioning)

**Examples**:

- ✅ `code-review-process.instruction.md`
- ✅ `ci-pipeline-setup.instruction.md`
- ✅ `security-scanning.instruction.md`
- ❌ `CodeReview.instruction.md` (not lowercase)
- ❌ `setup.md` (missing `.instruction.md` suffix)
- ❌ `instructions-v2.instruction.md` (version in filename)

## Standard Sections

Every instruction file should contain these sections in order:

1. **Title** - Clear, descriptive heading
2. **Introduction** - Purpose and when to use this instruction
3. **Audience and Scope** - Who uses it and what it covers
4. **Prerequisites** - Required tools, permissions, or knowledge
5. **Output Requirements** - Expected outputs and formats
6. **Steps / Procedure** - Ordered, actionable steps
7. **Examples / Samples** - Concrete, minimal examples
8. **Templates / Snippets** - Copy-paste blocks for reuse
9. **Validation / Quality Checklist** - Verification criteria
10. **File Placement and Naming** - Where outputs go (if applicable)
11. **Versioning and Ownership** - Maintenance information
12. **Security and Privacy** - Data handling guidelines
13. **References / Glossary** - Links and definitions (optional)

## Copy/Paste Template

Use this template as a starting point for new instruction files:

````markdown
# [Instruction Title]

## Introduction

[Brief purpose, when to use, and expected outcomes.]

## Audience and Scope

- **Audience**: [Who should use this]
- **Scope**: [What's included]
- **Out of scope**: [What's not covered]

## Prerequisites

- [Tool/permission/context required]
- [Another prerequisite]

## Output Requirements

- [Exact output path(s)/formats]
- [No extra commentary/wrappers]
- [Constraints or standards to follow]

## Steps / Procedure

1. [First step with clear action]
   - [Sub-step if needed]
   - [Expected outcome]
2. [Second step]
3. [Third step]

## Examples / Samples

```example
[Short, complete, runnable/renderable example]
```

## Templates / Snippets

```markdown
[Reusable snippet or section that users can copy]
```

## Validation / Quality Checklist

- [ ] Outputs and formats match requirements
- [ ] Steps are actionable and properly ordered
- [ ] Examples run or render as expected
- [ ] No secrets or sensitive data included
- [ ] Naming and placement adhere to conventions
- [ ] All prerequisites are documented

## File Placement and Naming

- **Folder**: `.github/instructions/`
- **Filename**: `<topic-name>.instruction.md`
- [Any additional placement rules specific to this instruction]

## Versioning and Ownership

- **Owner**: [name/role]
- **Created**: YYYY-MM-DD
- **Last updated**: YYYY-MM-DD
- **Change policy**: [How to propose changes - e.g., "Submit PR with description"]

## Security and Privacy

- [Redaction guidelines]
- [Secrets handling instructions]
- [PII (Personally Identifiable Information) guidance]
- [Reference to organization security policies]

## References / Glossary

- [Link to related documentation]
- **[Term]**: [Definition]
````

## Section-by-Section Guidance

### Title

- Be specific and reflect the topic accurately
- Use title case
- Examples: "CI Pipeline Instructions", "Code Review Process", "Security Scanning Setup"

### Introduction

- Explain what this instruction covers in 2-3 sentences
- State when contributors should use this instruction
- Describe the expected outcome

### Audience and Scope

- Clearly identify who should read and follow this instruction
- List what topics are included
- Explicitly state what's out of scope to prevent confusion

### Prerequisites

- List only what's truly required before starting
- Include tools with version requirements if applicable
- Link to setup documentation where relevant
- Mention required permissions or access levels

### Output Requirements

- State exact paths and formats for any generated files
- Specify whether files should be created or overwritten
- Define formatting standards (JSON, YAML, Markdown, etc.)
- Clarify what should NOT be included in outputs

### Steps / Procedure

- Use numbered lists for sequential steps
- One clear action per step
- Include sub-steps using indented bullets when needed
- Specify expected outcomes or verification steps
- Use imperative mood ("Run the command", not "You should run")

### Examples / Samples

- Keep minimal and focused on the essential concepts
- Ensure examples are runnable or renderable
- Use realistic but safe data (no real credentials)
- Include both successful and common error cases when helpful

### Templates / Snippets

- Provide complete, copy-paste ready blocks
- Use clear placeholders like `<description>` or `[value]`
- Add brief inline comments to explain non-obvious sections
- Format properly for the target language or format

### Validation / Quality Checklist

- Focus on verifiable, objective criteria
- Keep it short and scannable (5-10 items max)
- Use checkbox format for easy tracking
- Include both technical and documentation quality checks

### File Placement and Naming

- Specify exact directory paths
- Provide naming conventions with examples
- Clarify any special organizational rules

### Versioning and Ownership

- Name an accountable owner (individual or team)
- Require date stamps for tracking
- Define how changes should be proposed and reviewed
- Consider semantic versioning for major procedural changes

### Security and Privacy

- Explicitly forbid including secrets, tokens, or API keys
- Show redacted examples using `[REDACTED]` or similar
- Reference organizational security policies
- Provide guidance on handling sensitive data in examples

### References / Glossary

- Link to related documentation
- Define technical terms and acronyms
- Include links to tools, frameworks, or external resources

## Example

Here's a concise example instruction file:

````markdown
# Linting Instructions

## Introduction

This instruction guides contributors through running and interpreting code linting locally before submitting pull requests. Following these steps ensures code quality standards are met and reduces CI/CD failures.

## Audience and Scope

- **Audience**: All contributors submitting code changes
- **Scope**: Local linting setup and execution for C# projects
- **Out of scope**: CI/CD pipeline configuration, custom rule creation

## Prerequisites

- .NET 8.0 SDK or later installed
- Repository cloned locally
- Code editor (VS Code or Visual Studio recommended)

## Output Requirements

- Console output showing lint results
- Exit code 0 for passing lints
- No uncommitted files after auto-fixes

## Steps / Procedure

1. Navigate to the project root directory
2. Restore dependencies:
   ```bash
   dotnet restore
   ```
3. Run the linter:
   ```bash
   dotnet format --verify-no-changes
   ```
4. Review any reported issues
5. Apply automatic fixes:
   ```bash
   dotnet format
   ```
6. Re-run verification to confirm all issues are resolved
7. Commit any formatting changes

## Examples / Samples

**Passing lint:**

```text
$ dotnet format --verify-no-changes
✔ No formatting changes required
```

**With issues:**

```text
$ dotnet format --verify-no-changes
  Calculator.cs(15,5): Fix whitespace formatting.
  Calculator.cs(42,1): Remove unnecessary using directive.
Run `dotnet format` to apply fixes.
```

## Templates / Snippets

Add to your pre-commit script:

```bash
#!/bin/bash
dotnet format --verify-no-changes || {
  echo "Formatting issues detected. Run 'dotnet format' to fix."
  exit 1
}
```

## Validation / Quality Checklist

- [ ] Linter runs without errors
- [ ] Exit code is 0
- [ ] All auto-fixes are committed
- [ ] No new warnings introduced

## File Placement and Naming

Not applicable - this instruction doesn't generate files.

## Versioning and Ownership

- **Owner**: Development Team Lead
- **Created**: 2025-10-22
- **Last updated**: 2025-10-22
- **Change policy**: Submit PR for review

## Security and Privacy

- Linter output may contain file paths; do not share screenshots with sensitive directory names
- No credentials or secrets are handled by linting tools

## References / Glossary

- [.NET Format Documentation](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-format)
- **Linter**: A tool that analyzes code for potential errors and style violations
````

## Quality Checklist for Instruction Authors

Use this checklist before finalizing your instruction file:

- [ ] Title clearly describes the instruction topic
- [ ] Introduction explains purpose and when to use
- [ ] Audience and scope are explicitly defined
- [ ] All prerequisites are listed and linked where applicable
- [ ] Steps are ordered, actionable, and testable
- [ ] Examples are minimal but complete and actually work
- [ ] Templates/snippets are copy-paste ready
- [ ] Validation checklist is included and practical
- [ ] File is stored under `.github/instructions/`
- [ ] Filename uses kebab-case and ends with `.instruction.md`
- [ ] Owner and dates are specified
- [ ] Security/privacy guidance is appropriate for the topic
- [ ] Markdown renders correctly in preview
- [ ] No secrets, tokens, or real PII included
- [ ] All links work and point to correct resources

## Versioning and Ownership

- **Owner**: johnmillerATcodemag-com
- **Created**: 2025-10-22
- **Last updated**: 2025-10-22
- **Change policy**: Submit pull request with clear description of changes and rationale. Owner will review and merge if appropriate.

## Security and Privacy

When creating instruction files:

- **Never include**: API keys, passwords, tokens, connection strings, or any credentials
- **Redact examples**: Use `[REDACTED]`, `<your-token>`, or `***` for sensitive fields
- **PII handling**: Do not use real names, emails, or personal data in examples
- **File paths**: Be cautious with file paths that might reveal organizational structure
- **Code samples**: Review for accidentally committed secrets before including

If your instruction involves handling sensitive data:

- Reference the organization's security policy
- Provide clear guidelines on secure handling
- Show examples with obviously fake/redacted data

## References / Glossary

- **Instruction file**: A Markdown document providing step-by-step guidance for a specific task or process
- **Kebab-case**: Lowercase words separated by hyphens (e.g., `my-file-name`)
- **Frontmatter**: Metadata at the beginning of a file (YAML or TOML format)
- **Self-contained**: Document includes all necessary information without requiring external context
- **PII**: Personally Identifiable Information - any data that could identify a specific individual

### Related Documentation

- [Markdown Guide](https://www.markdownguide.org/)
- [GitHub Flavored Markdown Spec](https://github.github.com/gfm/)
