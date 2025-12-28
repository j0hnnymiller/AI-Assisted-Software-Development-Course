# Git Hooks

This directory contains custom Git hooks for the project.

## Installation

To enable these hooks, run:

```powershell
# PowerShell
git config core.hooksPath .githooks
```

```bash
# Bash/Zsh
git config core.hooksPath .githooks
chmod +x .githooks/*
```

## Available Hooks

### pre-commit

Runs before each commit to validate:

- .NET 8.0 SDK version compliance
- No .NET 9 features in code
- Project file constraints
- CI/CD configuration

**Bypass** (use only when necessary):

```bash
git commit --no-verify
```

## Disabling Hooks

To disable custom hooks:

```bash
git config --unset core.hooksPath
```
