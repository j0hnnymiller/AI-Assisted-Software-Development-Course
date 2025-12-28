---
applyTo: "**/*.cs"
triggers:
  - "C# style"
  - "C# naming conventions"
  - "C# code formatting"
  - "C# coding standards"
  - "class naming"
  - "method naming"
---

# C# Style and Naming Conventions

## AI Assistant Guidelines

- **CRITICAL**: Use .NET 8.0 SDK and C# 12 ONLY - do not suggest .NET 9.0 or C# 13
- Follow modern C# 12 conventions with file-scoped namespaces
- Use PascalCase for classes, methods, properties, and public members
- Use camelCase for parameters and local variables
- Use \_camelCase (with underscore prefix) for private fields
- Enable nullable reference types (`<Nullable>enable</Nullable>`)
- Use implicit usings where appropriate (`<ImplicitUsings>enable</ImplicitUsings>`)
- Prefer expression-bodied members for simple methods and properties
- Use XML documentation comments for public APIs

## C# Naming Conventions

### Classes and Types

**Format**: PascalCase

**Examples**:

```csharp
✅ public class Evaluator { }
✅ public static class Program { }
✅ public class MainWindow : Window { }
✅ public class AppSettings { }
```

**Rules**:

- Descriptive, noun-based names
- No abbreviations unless widely understood (e.g., `ID`, `URL`)
- Use full words: `Evaluator` not `Eval`, `Calculator` not `Calc`

### Methods

**Format**: PascalCase, verb-based

**Examples**:

```csharp
✅ public static decimal Evaluate(string expression)
✅ private static string ReplaceFunctions(string expression)
✅ public static List<(string Expression, decimal Result)> GetHistory()
✅ internal static void ResetMemory()
✅ private void EvaluateCurrentExpression()
✅ private void RefreshHistory()
```

**Rules**:

- Start with verb: `Evaluate`, `Replace`, `Get`, `Reset`, `Refresh`
- Describe action performed
- Boolean-returning methods often start with `Is`, `Has`, `Can`, `Should`

### Properties

**Format**: PascalCase, noun-based

**Examples**:

```csharp
✅ public required string Configuration { get; set; }
✅ public string Text { get; set; }
✅ public decimal Value { get; private set; }
```

**Auto-Properties Preferred**:

```csharp
✅ public string Name { get; set; }
✅ public int Count { get; private set; }
```

### Fields

**Format**:

- Private fields: `_camelCase` (underscore prefix)
- Constants: `PascalCase` or `UPPER_CASE`
- Static readonly: `PascalCase`

**Examples**:

```csharp
✅ private static decimal? _lastResult = null;
✅ private static readonly List<(string Expression, decimal Result)> _history = new();
✅ private const string AnsToken = "ANS";
```

**Rules**:

- Private instance fields: `_camelCase`
- Private static fields: `_camelCase`
- Constants: `PascalCase` (prefer) or `UPPER_CASE` (for true constants)
- Don't use `m_` or `s_` prefixes (outdated convention)

### Parameters and Local Variables

**Format**: camelCase

**Examples**:

```csharp
✅ public static decimal Evaluate(string expression)
✅ private static string ReplaceFunctions(string expression)
✅ var result = Evaluator.Evaluate("2+2");
✅ var originalExpression = expression;
✅ int startIdent = i;
✅ decimal innerResultDecimal = Evaluate(inner);
```

**Rules**:

- Descriptive names for parameters
- Use `var` for local variables when type is obvious
- Avoid single-letter names except for loop counters (`i`, `j`, `k`)

### Namespaces

**Format**: PascalCase, hierarchical

**Examples**:

```csharp
✅ namespace CalculatorLib
✅ namespace Calculator.Wpf
✅ namespace calculator.tests
✅ namespace Calculator.Tests
```

**File-Scoped Namespaces (Preferred in C# 10+)**:

```csharp
✅ namespace CalculatorLib;

public class Evaluator
{
    // ...
}
```

**Traditional Namespaces (Legacy)**:

```csharp
namespace CalculatorLib
{
    public class Evaluator
    {
        // ...
    }
}
```

### Interfaces

**Format**: PascalCase with `I` prefix

**Examples**:

```csharp
✅ public interface ICalculator { }
✅ public interface IExpressionEvaluator { }
```

**Note**: No interfaces in current calculator project

### Enums

**Format**: PascalCase for enum name and values

**Examples**:

```csharp
✅ public enum OperationType
   {
       Addition,
       Subtraction,
       Multiplication,
       Division
   }
```

**Note**: No enums in current calculator project

## Code Formatting

### Indentation and Spacing

**Indentation**: 4 spaces (not tabs)

**Braces**: Opening brace on same line (K&R style) or new line (Allman style)

- Current project uses **Allman style** (new line)

**Examples**:

```csharp
✅ Allman Style (Current Project):
public static decimal Evaluate(string expression)
{
    if (string.IsNullOrWhiteSpace(expression))
    {
        throw new ArgumentException("Empty expression");
    }
    return result;
}

✅ K&R Style (Alternative):
public static decimal Evaluate(string expression) {
    if (string.IsNullOrWhiteSpace(expression)) {
        throw new ArgumentException("Empty expression");
    }
    return result;
}
```

**Consistency**: Choose one style and stick with it (project uses Allman)

### Line Length

- **Preferred**: ≤ 120 characters
- **Maximum**: 150 characters
- Break long lines at logical points (after operators, parameters)

### Blank Lines

- One blank line between methods
- One blank line between logical sections within methods
- No blank line between property declarations (unless grouping suggests it)

### Using Directives

**Order** (automatic with .NET format):

1. `System.*` namespaces
2. Third-party namespaces
3. Project namespaces

**Implicit Usings Enabled**:

```csharp
// Don't need to declare these (implicit):
// using System;
// using System.Collections.Generic;
// using System.Linq;
```

**Explicit Usings** (when needed):

```csharp
using System.Data;
using System.Globalization;
using CalculatorLib;
```

## Modern C# Features

### Nullable Reference Types

**Enabled**: `<Nullable>enable</Nullable>`

**Usage**:

```csharp
✅ private static decimal? _lastResult = null;  // Nullable value type
✅ public required string Configuration { get; set; }  // Required reference type
✅ var line = Console.ReadLine();  // string? (nullable)
✅ if (line == null) return;  // Null check before use
```

**Null-Forgiving Operator** (use sparingly):

```csharp
⚠️ var text = obj!.ToString();  // Only when you're certain it's not null
```

### File-Scoped Namespaces

**Preferred** (C# 10+):

```csharp
✅ namespace CalculatorLib;

public class Evaluator { }
```

**Legacy**:

```csharp
namespace CalculatorLib
{
    public class Evaluator { }
}
```

### Pattern Matching

**Switch Expressions**:

```csharp
✅ var result = obj switch
   {
       double d => Convert.ToDecimal(d),
       decimal m => m,
       int i => i,
       long l => l,
       string s when decimal.TryParse(s, ...) => x,
       _ => Convert.ToDecimal(obj)
   };
```

**Is Patterns**:

```csharp
✅ if (sender is System.Windows.Controls.Button b)
   {
       var content = b.Content?.ToString();
   }
```

### String Interpolation

**Preferred over Concatenation**:

```csharp
✅ Console.WriteLine($"{i + 1}: {entry.Expression} = {entry.Result}");
✅ ResultTextBlock.Text = $"Error: {ex.Message}";

❌ Console.WriteLine((i + 1) + ": " + entry.Expression + " = " + entry.Result);
```

### Expression-Bodied Members

**For Simple Methods**:

```csharp
✅ public string GetName() => _name;
✅ public override string ToString() => $"Result: {_value}";
```

**For Properties**:

```csharp
✅ public int Count => _items.Count;
✅ public bool IsEmpty => Count == 0;
```

### Object Initializers

**Collection Initializers**:

```csharp
✅ private static readonly List<(string, decimal)> _history = new();
✅ var list = new List<string> { "item1", "item2" };
```

**Required Members** (C# 11+):

```csharp
✅ public class AppSettings
   {
       public required string Configuration { get; set; }
   }
```

## XML Documentation Comments

### Public APIs

**Always document public classes and methods**:

```csharp
/// <summary>
/// Provides evaluation of arithmetic expressions with support for functions,
/// ANS memory, and calculation history.
/// </summary>
/// <remarks>
/// Supported functions: sqrt, sin, cos, tan, log, ln.
/// The token "ANS" refers to the last evaluated result.
/// </remarks>
public static class Evaluator
{
    /// <summary>
    /// Evaluate the provided expression and return the numeric result.
    /// </summary>
    /// <param name="expression">Arithmetic expression to evaluate.</param>
    /// <returns>Decimal result of the evaluation.</returns>
    /// <exception cref="ArgumentException">Thrown for invalid input.</exception>
    /// <exception cref="InvalidOperationException">Thrown when ANS is referenced before any calculation.</exception>
    public static decimal Evaluate(string expression)
    {
        // ...
    }
}
```

**XML Tags**:

- `<summary>`: Brief description
- `<param name="...">`: Parameter description
- `<returns>`: Return value description
- `<exception cref="...">`: Exceptions thrown
- `<remarks>`: Additional details
- `<example>`: Usage examples

### Private Methods

**Optional but recommended for complex logic**:

```csharp
/// <summary>
/// Replaces function calls with their numeric results.
/// </summary>
private static string ReplaceFunctions(string expression)
{
    // ...
}
```

## Code Quality Patterns

### Prefer Readonly

```csharp
✅ private static readonly List<(string, decimal)> _history = new();
✅ private const string AnsToken = "ANS";
```

### Use var Appropriately

**Use `var`** when type is obvious:

```csharp
✅ var result = Evaluator.Evaluate("2+2");  // Clearly returns decimal
✅ var dt = new DataTable();  // Type is explicit
✅ var expression = string.Join(" ", args);  // Returns string
```

**Explicit type** when clarity needed:

```csharp
✅ decimal result = PerformCalculation();  // Clarifies return type
✅ IEnumerable<string> items = GetItems();  // Interface type important
```

### Exception Handling

See `error-handling-instructions.md` for detailed exception patterns.

**Quick Reference**:

```csharp
✅ if (string.IsNullOrWhiteSpace(expression))
       throw new ArgumentException("Empty expression");

✅ if (!_lastResult.HasValue)
       throw new InvalidOperationException("ANS referenced before any calculation");
```

## File Organization

### File Naming

**Rule**: File name matches primary class name

**Examples**:

- ✅ `Evaluator.cs` contains `class Evaluator`
- ✅ `Program.cs` contains `class Program`
- ✅ `MainWindow.xaml.cs` contains `partial class MainWindow`
- ✅ `UnitTest1.cs` contains `class UnitTest1`

### File Structure

**Order**:

1. Using directives
2. Namespace declaration
3. Classes, structs, enums
4. (Within class): Fields → Constructors → Properties → Methods

**Example**:

```csharp
using System;
using System.Data;

namespace CalculatorLib;

public static class Evaluator
{
    // Fields
    private static decimal? _lastResult = null;
    private static readonly List<(string, decimal)> _history = new();

    // Methods
    public static decimal Evaluate(string expression) { }
    private static string ReplaceFunctions(string expression) { }
}
```

## See Also

- **Project Structure**: project-instructions.md (C# version and framework details)
- **Testing Conventions**: csharp-testing-instructions.md (test-specific C# naming)
- **Error Handling**: error-handling-instructions.md (exception usage patterns)
- **Style Guidelines**: style-instructions.md (project-level file organization)
- **Best Practices**: practices-instructions.md (code quality standards)

**Note**: This file provides detailed C# language-specific naming conventions and coding standards. For project-level file organization and cross-technology patterns, refer to `style-instructions.md` and `project-instructions.md`.
