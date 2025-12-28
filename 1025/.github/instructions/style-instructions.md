---
applyTo: "**"
triggers:
  - "code style"
  - "project naming"
  - "file organization"
  - "directory structure"
---

# Project-Level Style and Organization

## AI Assistant Guidelines

- **CRITICAL**: Use .NET 8.0 SDK ONLY - do not suggest .NET 9.0 or other versions
- Follow project-level file and directory naming conventions
- Maintain consistency with existing project structure
- Use meaningful, descriptive names for files and directories
- Apply appropriate organization patterns based on project architecture
- **Technology-specific conventions override general conventions** - refer to technology-specific files for detailed guidance

## Naming Convention Precedence Rules

**IMPORTANT**: When naming conventions conflict, follow this hierarchy:

1. **Technology-Specific Instructions** (highest priority)

   - C# naming conventions (PascalCase for classes, methods)
   - WPF naming conventions (XAML file naming, code-behind patterns)
   - MSTest naming conventions (test class and method names)

2. **Project-Level Conventions** (medium priority)

   - Cross-technology consistency patterns
   - Project-specific organizational patterns

3. **General Guidelines** (lowest priority)
   - Industry standards and common practices

## Project-Level File Organization

### Root Directory Structure

```
AI-Assisted-Software-Development-1025/
├── calculator/                          # Core CLI project
├── calculator-wpf/                      # WPF desktop project
├── calculator-net9/                     # .NET 9 experimental project
├── calculator.tests/                    # Test project
├── .github/                             # GitHub configuration
│   ├── instructions/                    # This instruction system
│   ├── workflows/                       # CI/CD pipelines
│   ├── ISSUES/                          # Issue templates and tracking
│   └── prompts/                         # AI prompt templates
├── coverage-report/                     # Generated coverage reports (git-ignored in production)
├── *.sln                                # Solution file
├── NuGet.Config                         # NuGet configuration
└── *.md                                 # Documentation files
```

### Directory Naming Conventions

#### Project Directories

- **Format**: `lowercase-with-hyphens` for multi-word project names
- **Examples**:
  - ✅ `calculator-wpf/` - WPF calculator project
  - ✅ `calculator-net9/` - .NET 9 experimental project
  - ✅ `calculator.tests/` - Test project (using .NET convention)
  - ✅ `calculator/` - Core calculator project

**Rationale**: Cross-platform compatibility, standard .NET conventions

#### Hidden/Configuration Directories

- **Format**: `.lowercase` for hidden/system directories
- **Examples**:
  - ✅ `.github/` - GitHub configuration
  - ✅ `.vs/` - Visual Studio settings (git-ignored)
  - ✅ `.vscode/` - VS Code settings

#### Generated Output Directories

- **Format**: `lowercase-with-hyphens`
- **Examples**:
  - ✅ `coverage-report/` - Generated HTML coverage reports
  - ✅ `bin/` - Build output (standard .NET convention)
  - ✅ `obj/` - Intermediate build files (standard .NET convention)

### Cross-Technology File Naming

#### Documentation Files

**UPPERCASE for major documentation**:

- ✅ `README.md` - Primary project documentation
- ✅ `DOCUMENTATION.md` - Documentation index
- ✅ `DIAGRAMS.md` - Architecture diagrams
- ✅ `SETUP.md` - Main setup guide
- ✅ `SETUP-WINDOWS.md` - Platform-specific setup
- ✅ `LICENSE` - License file
- ✅ `ROTATION.md` - Mob programming rotation guide

**lowercase-with-hyphens for reports and secondary docs**:

- ✅ `test-gap-analysis-report.md`
- ✅ `dotnet-9-upgrade-plan.md`
- ✅ `pr-body.md`

**Rationale**: UPPERCASE for important entry-point docs, lowercase for generated/secondary content

#### Solution and Configuration Files

- ✅ `AI-Assisted-Software-Development-1025.sln` - Solution file (matches repo name)
- ✅ `NuGet.Config` - NuGet configuration (PascalCase per .NET convention)
- ✅ `.gitignore` - Git ignore rules
- ✅ `.editorconfig` - Editor configuration (if present)

#### Project Files

See `csharp/csharp-style-instructions.md` for detailed C# project file naming conventions.

### GitHub-Specific Organization

#### `.github/` Directory Structure

```
.github/
├── instructions/                        # Copilot instruction files
│   ├── README.md                        # Instruction system overview
│   ├── project-instructions.md          # Project architecture and tech stack
│   ├── practices-instructions.md        # Best practices
│   ├── error-handling-instructions.md   # Error handling strategy
│   ├── style-instructions.md            # This file
│   ├── testing-instructions.md          # Testing strategy
│   ├── csharp/                          # C#-specific instructions
│   └── wpf/                             # WPF-specific instructions
├── workflows/                           # GitHub Actions workflows
│   └── ci.yml                           # CI/CD pipeline
├── ISSUES/                              # Issue templates
└── prompts/                             # AI prompt templates
    └── *.prompt.md                      # Prompt files
```

**Naming Rules**:

- Instruction files: `lowercase-with-hyphens-instructions.md`
- Workflow files: `lowercase.yml` or `lowercase-with-hyphens.yml`
- Prompt files: `descriptive-name.prompt.md`

### Generated and Build Artifacts

#### Coverage Reports

```
coverage-report/
├── index.html                           # Main coverage report
├── *.html                               # Per-file coverage reports
├── *.js                                 # Report scripts
└── *.css                                # Report styles
```

**Naming**: Generated by coverage tools; don't modify manually

#### Build Outputs (Standard .NET)

```
project-name/
├── bin/                                 # Build outputs
│   ├── Debug/                           # Debug builds
│   │   └── net8.0/                      # Target framework
│   └── Release/                         # Release builds
│       └── net8.0/
└── obj/                                 # Intermediate files
    ├── *.csproj.nuget.*                 # NuGet metadata
    ├── project.assets.json              # Project dependencies
    └── Debug/                           # Debug intermediate files
```

**Note**: These are standard .NET conventions; do not deviate

### Script and Tool Files

- `mob-auto` - Mob programming script (no extension)
- `mob-ssh.bat` - Windows batch file for mob over SSH

**Naming**: Lowercase, descriptive, with appropriate extension

## File Organization Patterns

### Feature-Based vs. Technology-Based Organization

**Current Approach**: Technology-based (separate projects by technology)

```
calculator/           # CLI/Core technology
calculator-wpf/       # WPF technology
calculator-net9/      # .NET 9 experimental
calculator.tests/     # Testing technology
```

**Rationale**:

- Clear separation of concerns
- Independent build and deployment
- Technology-specific dependencies isolated

### Shared Resources and Assets

**Core Business Logic**: Shared via project references

```xml
<ProjectReference Include="..\calculator\Calculator.csproj" />
```

**No Shared Assets**: Each project is self-contained

- CLI has no assets
- WPF has UI resources in its project
- Tests reference both projects

### Build and Deployment Artifacts

**Git Ignore Strategy**:

- ✅ Ignore `bin/` and `obj/` directories
- ✅ Ignore `coverage-report/` (generated in CI)
- ✅ Ignore `.vs/` and user-specific files
- ✅ Commit solution and project files
- ✅ Commit source code and XAML

## Integration with Technology-Specific Guidelines

This file provides project-level organization. For detailed naming conventions:

- **C# Files**: csharp/csharp-style-instructions.md
- **WPF Files**: wpf/wpf-instructions.md
- **Test Files**: csharp/csharp-testing-instructions.md

## Conflict Resolution

When technology conventions conflict:

1. **Consult technology-specific instruction files first**
2. **Maintain consistency within each technology domain**
3. **Use project-level conventions only for cross-technology files**
4. **Document exceptions and rationale** in relevant instruction files

### Example Conflicts and Resolutions

**Scenario**: Should a new utilities class go in `calculator/` or a new `utilities/` project?

**Resolution**:

- If it's core calculation logic → `calculator/` project
- If it's UI-specific → appropriate UI project
- Don't create new projects without strong justification

**Scenario**: Should documentation use PascalCase or lowercase-with-hyphens?

**Resolution**: Follow the established pattern:

- Major docs (README, SETUP, etc.) → UPPERCASE
- Reports and secondary docs → lowercase-with-hyphens

## See Also

- **Project Structure**: project-instructions.md (overall architecture and organization principles)
- **Technology Stack**: project-instructions.md (authoritative technology list)
- **C# Style**: csharp/csharp-style-instructions.md (C# file and class naming)
- **WPF Style**: wpf/wpf-instructions.md (XAML and code-behind naming)
- **Testing Style**: csharp/csharp-testing-instructions.md (test file organization)

**Note**: This file focuses on project-level file and directory organization. For detailed naming conventions, coding standards, and formatting rules specific to each technology, refer to the appropriate technology-specific instruction files.
