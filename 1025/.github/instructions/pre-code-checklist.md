---
applyTo: "**"
triggers:
  - "new"
  - "create"
  - "add"
  - "implement"
  - "generate"
  - "feature"
---

# Pre-Code Generation Checklist

## ⚠️ MANDATORY: Check Before ANY Code Generation

**AI Assistants: Read this file BEFORE creating/modifying ANY code.**

### Step 1: Verify Project Constraints ✅

```markdown
- [ ] Project uses .NET 8.0 ONLY (not .NET 9.0)
- [ ] No .NET 9 features will be used
- [ ] Feature is compatible with C# 12 (not C# 13)
- [ ] No preview/experimental features required
```

### Step 2: Check Compatibility 🔍

**If user requests a .NET 9 feature, STOP and:**

1. ❌ Do NOT implement the .NET 9 version
2. ✅ Explain: "This project uses .NET 8.0, which doesn't support [feature]"
3. ✅ Offer: "I can show you the .NET 8.0 equivalent approach"
4. ✅ Suggest: "Or I can create a separate .NET 9 demo project"

### Step 3: Common .NET 9 Features to AVOID ❌

| .NET 9 Feature                     | ❌ Don't Use                  | ✅ Use Instead (.NET 8)                 |
| ---------------------------------- | ----------------------------- | --------------------------------------- |
| `params ReadOnlySpan<T>`           | `params ReadOnlySpan<double>` | `params double[]`                       |
| `params IEnumerable<T>`            | `params IEnumerable<string>`  | `params string[]`                       |
| `params List<T>`                   | `params List<int>`            | `params int[]` or `List<int>` parameter |
| Collection expression enhancements | Advanced spreading            | Traditional collection initialization   |
| LINQ improvements                  | New methods                   | .NET 8 LINQ methods                     |
| `Lock` type                        | `Lock` object                 | `lock (object)` statement               |

### Step 4: Validate After Changes ✅

After creating/modifying code:

```powershell
# Run constraint validation
.\verify-constraints.ps1

# Build to verify compatibility
dotnet build

# Run tests
dotnet test
```

### Step 5: Common Scenarios 📋

**Scenario: User asks "create a class demonstrating .NET 9 params"**

❌ **WRONG Response:**

```csharp
// Creating ParamsDemo.cs with .NET 9 features
public static void Demo(params ReadOnlySpan<int> numbers) { }
```

✅ **CORRECT Response:**

````markdown
I notice you're asking about .NET 9's params feature. However, this project
uses .NET 8.0 only.

.NET 8 supports params with arrays only:

```csharp
public static void Demo(params int[] numbers) { }
```
````

Would you like me to:

1. Create a demo of .NET 8 params capabilities
2. Show best practices for params in .NET 8
3. Create a separate .NET 9 demo project to explore new features

````

**Scenario: User asks "add the latest C# feature"**

✅ **CORRECT Response:**
```markdown
I'll check which features are available in C# 12 (.NET 8.0).
Before implementing, let me verify the specific feature you'd like...
````

## Reference Links

- **Project Version Info**: See [project-instructions.md](project-instructions.md#technology-stack-authoritative-reference)
- **Build Validation**: Run `.\verify-constraints.ps1`
- **CI/CD Check**: `.github/workflows/ci.yml` validates constraints

## Quick Constraint Summary

```yaml
Required:
  SDK: .NET 8.0 (8.0.x)
  Language: C# 12
  Target Frameworks: net8.0, net8.0-windows

Forbidden:
  SDK: .NET 9.0 or higher
  Language: C# 13 features
  Features: Any .NET 9+ exclusive features
```

---

**Remember: When in doubt, check `project-instructions.md` for authoritative technology stack!**
