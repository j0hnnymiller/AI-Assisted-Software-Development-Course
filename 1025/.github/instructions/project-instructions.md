---
applyTo: "**"
triggers:
  - "project structure"
  - "architecture overview"
  - "technology stack"
  - "project organization"
  - "system design"
  - "new class"
  - "new file"
  - "create"
  - ".NET"
---

# Project Structure and Architecture Instructions

## ⚠️ CRITICAL CONSTRAINTS - READ FIRST ⚠️

**BEFORE generating ANY code, creating files, or suggesting features:**

1. ✅ **VERIFY**: This project uses .NET 8.0 SDK ONLY
2. ❌ **DO NOT**: Use .NET 9.0 features (params collections, collection expressions enhancements, etc.)
3. ✅ **CHECK**: Read this file completely before implementing requests
4. ✅ **VALIDATE**: Build the project after making changes to verify compatibility

**If a user requests a .NET 9 feature:**

- Politely explain this project uses .NET 8.0
- Offer to demonstrate the .NET 8.0 equivalent
- Suggest creating a separate .NET 9 demo project if they want to explore new features

## AI Assistant Guidelines

- Follow established three-project structure when creating new files
- **CRITICAL**: Use .NET 8.0 SDK ONLY - do not suggest .NET 9.0 or other versions
- Use .NET 8.0 SDK consistently across all projects (authoritative version defined below)
- Maintain clear separation between UI layers (CLI, WPF) and core business logic
- Reference technology-specific instruction files for detailed naming conventions and coding patterns
- Consider integration patterns between CLI, WPF, and core library when making suggestions
- Use the technology stack versions defined in this file as the single source of truth

## Project Overview

**Name:** AI-Assisted-Software-Development-1025 Calculator
**Type:** Educational project demonstrating TDD, CI/CD, and mob programming
**Architecture:** Monolithic with layered separation of concerns
**Development Methodology:** Test-Driven Development (TDD) with mob programming

## Technology Stack (Authoritative Reference)

**IMPORTANT**: This section is the single source of truth for all technology versions. All other instruction files reference these versions.

**CRITICAL**: This project uses .NET 8.0 ONLY. Do not use .NET 9.0 or any other version.

### Core Framework

- **.NET SDK**: 8.0 (REQUIRED - do not use .NET 9.0)
- **Language**: C# 12 (implicit with .NET 8.0)
- **Language Features**:
  - Implicit usings enabled
  - Nullable reference types enabled
  - File-scoped namespaces (modern C# pattern)

### UI Technologies

- **Console/CLI**: .NET 8.0 Console Application (`OutputType: Exe`)
- **WPF**: .NET 8.0 Windows Desktop (`TargetFramework: net8.0-windows`, `UseWPF: true`)

### Testing and Quality

- **Test Framework**: MSTest 2.2.10
  - `MSTest.TestFramework` 2.2.10
  - `MSTest.TestAdapter` 2.2.10
- **Test SDK**: Microsoft.NET.Test.Sdk 17.11.1
- **Code Coverage**: coverlet.collector 3.2.0
- **Coverage Target**: 50% minimum (CI/CD enforced), 80%+ recommended for critical paths
- **Current Coverage**: 77.72% line coverage, 70.27% branch coverage

### Build and DevOps

- **Build System**: MSBuild (SDK-style projects)
- **CI/CD Platform**: GitHub Actions
- **Version Control**: Git
- **Workflow Tool**: mob.sh (mob programming rotation)

### Runtime Dependencies

- **System.Data**: Built-in (.NET 8.0) - used for `DataTable.Compute()` expression evaluation
- **System.Math**: Built-in (.NET 8.0) - mathematical functions

## Project Structure

### Solution Structure

```
AI-Assisted-Software-Development-1025/
├── AI-Assisted-Software-Development-1025.sln    # Solution file
├── calculator/                                   # Core CLI project
│   ├── Calculator.csproj                        # Console app project
│   ├── Program.cs                               # CLI entry point + REPL
│   ├── Evaluator.cs                             # Core expression evaluator
│   └── Properties/
│       └── AssemblyInfo.cs                      # Assembly metadata
├── calculator-wpf/                              # WPF desktop project
│   ├── Calculator.Wpf.csproj                    # WPF project
│   ├── App.xaml / App.xaml.cs                   # WPF application entry
│   ├── MainWindow.xaml / MainWindow.xaml.cs     # Main calculator window
│   └── README.md                                # WPF-specific docs
├── calculator.tests/                            # Test project
│   ├── Calculator.Tests.csproj                  # Test project
│   ├── UnitTest1.cs                             # Core evaluator tests
│   ├── ProgramTests.cs                          # CLI tests
│   ├── MainWindowTests.cs                       # WPF tests
│   ├── MemoryTests.cs                           # ANS memory tests
│   ├── LogTests.cs                              # History/logging tests
│   └── LogCommandTests.cs                       # Log command tests
├── .github/
│   ├── instructions/                            # This instruction system
│   ├── workflows/
│   │   └── ci.yml                               # CI/CD pipeline
│   └── ISSUES/                                  # Issue tracking
├── coverage-report/                             # Generated coverage reports
├── README.md                                    # Main documentation
├── DOCUMENTATION.md                             # Documentation index
├── DIAGRAMS.md                                  # Architecture diagrams
├── test-gap-analysis-report.md                  # Test coverage analysis
└── SETUP*.md                                    # Setup guides
```

### Project Organization Principles

1. **Separation of Concerns**:

   - `calculator/` - Core business logic and CLI interface
   - `calculator-wpf/` - Desktop UI layer (depends on calculator)
   - `calculator.tests/` - Testing layer (depends on both calculator and calculator-wpf)

2. **Dependency Flow**:

   ```
   calculator.tests → calculator-wpf → calculator
                    ↘                 ↗
                      Core Evaluator Logic
   ```

3. **Shared Logic**:
   - `Evaluator.cs` contains all core calculation logic
   - Both CLI and WPF depend on the same `Evaluator` class
   - No UI-specific code in `Evaluator.cs`

## Architecture Patterns

### Layered Architecture

**Presentation Layer:**

- CLI/REPL (Program.cs)
- WPF Desktop (MainWindow)

**Business Logic Layer:**

- Expression Evaluator (Evaluator.cs)
- ANS Memory
- Calculation History

**Data/Infrastructure Layer:**

- System.Data.DataTable
- System.Math

### Key Design Decisions

1. **Static Evaluator Class**: Core `Evaluator` is static to maintain global state (ANS memory, history)
2. **Expression Parsing Strategy**: Hybrid approach using `DataTable.Compute()` for arithmetic and custom parsing for functions
3. **Function Replacement**: Functions (sqrt, sin, cos, etc.) are replaced with numeric values before final evaluation
4. **Memory Model**: Single ANS register + unbounded history list

### Cross-Technology Integration

#### CLI ↔ Core Library

- `Program.cs` calls `Evaluator.Evaluate(expression)`
- Error handling: Catch exceptions and write to `Console.Error`
- Result display: Write to `Console.Out` with invariant culture formatting

#### WPF ↔ Core Library

- `MainWindow.xaml.cs` calls `Evaluator.Evaluate(expression)`
- Error handling: Catch exceptions and display in `ResultTextBlock`
- Result display: Update `ResultTextBlock.Text` with result

#### Test ↔ All Components

- Tests use `[TestInitialize]` to reset `Evaluator` state before each test
- Tests can access both public APIs and internal reset methods
- Coverage collected via coverlet and reported to CI/CD

## Integration Considerations

### Data Flow

1. **User Input** → CLI or WPF
2. **Expression String** → `Evaluator.Evaluate()`
3. **Function Replacement** → `ReplaceFunctions()` recursively processes functions
4. **ANS Substitution** → Replace ANS token with last result value
5. **Arithmetic Evaluation** → `DataTable.Compute()` evaluates final numeric expression
6. **Result Storage** → Store in `_lastResult` (ANS) and `_history`
7. **Result Display** → Return to UI layer for presentation

### State Management

- **ANS Memory**: `Evaluator._lastResult` (nullable decimal)
- **Calculation History**: `Evaluator._history` (list of tuples)
- **State Isolation**: Tests reset state via `ResetMemory()` and `ResetHistory()`
- **Concurrency**: Not thread-safe; designed for single-user desktop application

### Error Handling Integration

**High-Level Strategy** (see [error-handling-instructions.md](error-handling-instructions.md) for details):

- **Validation Errors**: `ArgumentException` for invalid input (empty, mismatched parens, invalid chars)
- **Evaluation Errors**: `InvalidOperationException` for runtime issues (ANS before calculation, NaN/Infinity results)
- **UI Error Display**:
  - CLI: Write to `Console.Error` with "Error: " prefix
  - WPF: Display in result area with "Error: " prefix

## Configuration Management

### Build Configurations

- **Debug**: Default for development, includes debug symbols
- **Release**: Optimized build for production/distribution
- Both configurations produce same functional output

### Project References

```xml
<!-- calculator-wpf references calculator -->
<ProjectReference Include="..\calculator\Calculator.csproj" />

<!-- calculator.tests references both projects -->
<ProjectReference Include="..\calculator\Calculator.csproj" />
<ProjectReference Include="..\calculator-wpf\Calculator.Wpf.csproj" />
```

### NuGet Configuration

- **Package Source**: NuGet.org (default)
- **Package Management**: SDK-style `<PackageReference>` elements
- **Restore**: Automatic on build via `dotnet restore`

## File Naming Conventions

### Project Files

- **Project Files**: PascalCase with descriptive names (`Calculator.csproj`, `Calculator.Wpf.csproj`)
- **Source Files**: PascalCase matching primary class name (`Evaluator.cs`, `Program.cs`, `MainWindow.xaml.cs`)
- **Test Files**: PascalCase with `Tests` suffix (`ProgramTests.cs`, `MemoryTests.cs`)
- **XAML Files**: PascalCase matching code-behind (`MainWindow.xaml` + `MainWindow.xaml.cs`)

### Documentation Files

- **README Files**: UPPERCASE `README.md`
- **Documentation**: UPPERCASE for major docs (`DOCUMENTATION.md`, `SETUP.md`, `DIAGRAMS.md`)
- **Reports**: lowercase-with-hyphens (`test-gap-analysis-report.md`)

See [style-instructions.md](style-instructions.md) for detailed file organization guidance.

## Quick Reference

For detailed implementation guidance, see:

- **C# Coding Standards**: [csharp/csharp-style-instructions.md](csharp/csharp-style-instructions.md)
- **C# Testing Patterns**: [csharp/csharp-testing-instructions.md](csharp/csharp-testing-instructions.md)
- **WPF Patterns**: [wpf/wpf-instructions.md](wpf/wpf-instructions.md)
- **Error Handling**: [error-handling-instructions.md](error-handling-instructions.md)
- **Testing Strategy**: [testing-instructions.md](testing-instructions.md)
- **Best Practices**: [practices-instructions.md](practices-instructions.md)

**Note**: For technology-specific naming conventions, coding patterns, and detailed implementation guidelines, refer to the appropriate technology-specific instruction files in their respective subdirectories.
