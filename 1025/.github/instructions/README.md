---
applyTo: "**"
---

# Development Instructions

Comprehensive coding guidelines, templates, and best practices for the AI-Assisted-Software-Development-1025 Calculator project.

## AI Assistant Integration

This instruction system follows the GitHub Copilot Instruction File Format to provide context-aware guidance for AI-assisted development. Each instruction file includes:

- **applyTo**: File patterns where the instruction applies
- **triggers**: Keywords that should activate the instruction
- **AI Assistant Guidelines**: Specific guidance for AI-powered code generation
- **Cross-References**: Links to related instruction files for comprehensive context

## Project Overview

**Calculator Application** - A .NET 8.0 C# project with dual interfaces:

**IMPORTANT**: This project uses .NET 8.0 ONLY. Do not use .NET 9.0 or any other version.

- **CLI/REPL**: Command-line calculator with interactive mode
- **WPF Desktop**: Windows desktop calculator with visual interface

**Technology Stack:**

- .NET 8.0 (REQUIRED - do not use .NET 9.0)
- C# 12 (implicit with .NET 8.0)
- WPF for desktop UI
- MSTest 2.2.10 for testing
- GitHub Actions for CI/CD

**Architecture:** Monolithic with clear separation between UI layers (CLI, WPF) and core business logic (Evaluator library).

## Quick Reference

### ⚠️ Before Creating ANY Code

**AI Assistants: Read [pre-code-checklist.md](pre-code-checklist.md) FIRST!**

This file contains mandatory constraint checks to prevent version compatibility issues.

### General Instructions (Cross-Technology)

1. **[pre-code-checklist.md](pre-code-checklist.md)** - ⚠️ Mandatory pre-flight checks before code generation
2. **[project-instructions.md](project-instructions.md)** - Project structure, architecture, and authoritative technology stack
3. **[practices-instructions.md](practices-instructions.md)** - Development best practices and workflow
4. **[error-handling-instructions.md](error-handling-instructions.md)** - Error handling strategy and logging architecture
5. **[style-instructions.md](style-instructions.md)** - Project-level file and directory organization
6. **[testing-instructions.md](testing-instructions.md)** - Testing strategy, coverage standards, and quality gates

### Technology-Specific Instructions

#### C# Development

- **[csharp/csharp-style-instructions.md](csharp/csharp-style-instructions.md)** - C# naming conventions, coding standards, and formatting
- **[csharp/csharp-testing-instructions.md](csharp/csharp-testing-instructions.md)** - MSTest patterns and C# testing best practices

#### WPF Development

- **[wpf/wpf-instructions.md](wpf/wpf-instructions.md)** - WPF patterns, XAML guidelines, and desktop UI best practices

## How to Use These Instructions

### For AI Assistants

1. **Start with project-instructions.md** to understand the overall architecture and technology stack
2. **Consult technology-specific files** for detailed implementation guidance
3. **Reference general files** for cross-cutting concerns (error handling, testing, practices)
4. **Follow precedence rules**: Technology-specific conventions override general guidelines

### For Developers

1. Review **project-instructions.md** for project context and architecture
2. Check **style-instructions.md** for file organization principles
3. Consult **technology-specific files** for coding standards and patterns
4. Refer to **practices-instructions.md** and **testing-instructions.md** for quality standards

### Precedence Hierarchy

When conventions conflict, follow this order (highest to lowest priority):

1. **Technology-Specific Instructions** (e.g., C# naming conventions in csharp-style-instructions.md)
2. **Project-Level Conventions** (e.g., directory structure in style-instructions.md)
3. **General Industry Standards** (implied default best practices)

## Instruction File Categories

### Architecture & Structure

- **project-instructions.md**: Single source of truth for technology versions, architecture patterns, project structure
- **style-instructions.md**: File organization, directory structure, cross-technology naming

### Development Standards

- **practices-instructions.md**: Best practices, performance, security, code quality
- **error-handling-instructions.md**: Exception handling, logging, error recovery
- **testing-instructions.md**: Testing pyramid, coverage standards, quality gates

### Technology Implementation

- **csharp/**: C# language-specific patterns, conventions, and testing
- **wpf/**: WPF framework-specific UI patterns and XAML guidelines

## Maintenance Guidelines

### When Adding New Instruction Files

1. **Check for Duplication**: Ensure content doesn't duplicate existing files
2. **Establish Clear Scope**: Define precise boundaries for the new file's content
3. **Add Cross-References**: Link to related files in "See Also" sections
4. **Update This README**: Add the new file to the appropriate category

### When Updating Existing Files

1. **Maintain Single Source of Truth**: Don't duplicate authoritative information
2. **Update Cross-References**: Ensure links remain accurate
3. **Preserve Precedence Rules**: Technology-specific files should override general files
4. **Version Consistency**: Technology versions live only in project-instructions.md

## Contributing

When contributing to this instruction system:

1. Follow the existing template structure
2. Use precise `applyTo` patterns and relevant `triggers`
3. Include comprehensive AI Assistant Guidelines
4. Add clear cross-references to related files
5. Avoid content duplication across files
6. Test that instructions work with AI assistants

## Getting Help

- **Project Overview**: See [README.md](../../README.md) in repository root
- **Setup Instructions**: See [SETUP.md](../../SETUP.md) for development environment setup
- **Architecture Diagrams**: See [DIAGRAMS.md](../../DIAGRAMS.md) for visual architecture reference
- **Test Coverage**: See [test-gap-analysis-report.md](../../test-gap-analysis-report.md) for current testing status
