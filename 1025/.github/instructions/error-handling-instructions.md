---

applyTo: "**"
triggers:
  - "error handling strategy"
  - "exception handling"
  - "logging architecture"
  - "error reporting"
  - "error messages"
---

# Error Handling Strategy and Logging

## AI Assistant Guidelines

- **CRITICAL**: Use .NET 8.0 SDK ONLY - do not suggest .NET 9.0 or other versions
- Implement consistent error handling patterns across CLI and WPF layers
- Use appropriate exception types for different error conditions
- Provide user-friendly error messages while preserving technical details for debugging
- Follow validation-first approach: check inputs before attempting operations
- Do not log in current implementation (logging infrastructure not yet implemented)
- Focus on clear, actionable error messages for end users

## Error Handling Architecture

### Error Classification

The calculator uses a clear hierarchy of exception types:

1. **`ArgumentException`**: Invalid user input that should be caught early

   - Empty expressions
   - Mismatched parentheses
   - Invalid characters
   - Malformed expressions

2. **`InvalidOperationException`**: Runtime evaluation errors

   - ANS referenced before any calculation
   - NaN or Infinity results
   - Evaluation failures

3. **`OverflowException`**: Numeric overflow/underflow
   - Division by zero (results in Infinity → OverflowException)
   - Results exceeding decimal range
   - Conversion failures

### Exception Usage Patterns

#### Input Validation Errors (ArgumentException)

**When to Use**: User provided invalid input that violates syntax rules

**Examples**:

```csharp
// Empty expression
if (string.IsNullOrWhiteSpace(expression))
    throw new ArgumentException("Empty expression");

// Mismatched parentheses
if (depth != 0)
    throw new ArgumentException("Mismatched parentheses in function call");

// Invalid characters
if (char.IsLetter(c))
    throw new ArgumentException("Expression contains invalid characters");
```

**Message Guidelines**:

- Be specific about what's wrong
- Don't expose internal implementation details
- Use simple, clear language

#### Evaluation Errors (InvalidOperationException)

**When to Use**: Expression is syntactically valid but cannot be evaluated

**Examples**:

```csharp
// ANS before any calculation
if (!_lastResult.HasValue)
    throw new InvalidOperationException("ANS referenced before any calculation was performed");

// Invalid results
if (double.IsNaN(fnResult) || double.IsInfinity(fnResult))
    throw new InvalidOperationException("Function result is not a valid number");

// Evaluation failure
if (obj == null)
    throw new InvalidOperationException("Could not evaluate expression");
```

**Message Guidelines**:

- Explain the operational constraint that was violated
- Suggest what the user might do differently
- Be helpful, not accusatory

#### Numeric Overflow (OverflowException)

**When to Use**: Result exceeds numeric type bounds

**Examples**:

- Division by zero (detected as Infinity → overflow in conversion)
- Results exceeding `decimal.MaxValue`
- Conversion from `double` to `decimal` overflow

**Handling**: Let system throw naturally; catch and display in UI layer

### UI Layer Error Handling

#### CLI Error Handling Pattern

```csharp
internal static bool EvaluateAndPrint(string expression)
{
    try
    {
        var result = CalculatorLib.Evaluator.Evaluate(expression);
        Console.WriteLine(result.ToString(CultureInfo.InvariantCulture));
        return true;
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine($"Error: {ex.Message}");
        return false;
    }
}
```

**Key Points**:

- Catch all exceptions (calculator errors are expected)
- Write to `Console.Error` for error output
- Prefix with "Error: " for clarity
- Return `false` to indicate failure (exit code 1)
- Use invariant culture for numeric output

#### WPF Error Handling Pattern

```csharp
private void EvaluateCurrentExpression()
{
    var expr = ExpressionTextBox.Text?.Trim();
    if (string.IsNullOrEmpty(expr)) return;

    try
    {
        var result = Evaluator.Evaluate(expr);
        ResultTextBlock.Text = result.ToString();
    }
    catch (Exception ex)
    {
        ResultTextBlock.Text = "Error: " + ex.Message;
    }

    RefreshHistory();
}
```

**Key Points**:

- Catch all exceptions at UI event handlers
- Display error message in result area
- Prefix with "Error: " for consistency with CLI
- Always refresh history (even on error)
- Don't crash the application on calculation errors

### Error Message Standards

#### User-Facing Messages

**Do**:

- ✅ "Empty expression"
- ✅ "ANS referenced before any calculation was performed"
- ✅ "Mismatched parentheses in function call"
- ✅ "Expression contains invalid characters"
- ✅ "Function result is not a valid number"

**Don't**:

- ❌ "NullReferenceException in Evaluator.cs line 42"
- ❌ "System.Data.DataTable.Compute failed"
- ❌ "Unhandled exception of type System.InvalidOperationException"
- ❌ Technical stack traces

#### Error Message Format

```
Error: <clear description of what went wrong>
```

**Examples**:

```
Error: Empty expression
Error: ANS referenced before any calculation was performed
Error: Mismatched parentheses in function call
Error: Expression contains invalid characters
```

### Validation Strategy

#### Fail-Fast Validation

Validate inputs immediately, before attempting operations:

```csharp
public static decimal Evaluate(string expression)
{
    // 1. Validate not empty
    if (string.IsNullOrWhiteSpace(expression))
        throw new ArgumentException("Empty expression");

    // 2. Process and validate
    expression = ReplaceFunctions(expression);  // Validates parentheses

    // 3. Validate ANS usage
    if (expression.Contains(AnsToken, StringComparison.OrdinalIgnoreCase))
    {
        if (!_lastResult.HasValue)
            throw new InvalidOperationException("ANS referenced before any calculation");
    }

    // 4. Validate remaining characters
    foreach (var c in expression)
    {
        if (char.IsLetter(c))
            throw new ArgumentException("Expression contains invalid characters");
    }

    // 5. Attempt evaluation only after validation
    var result = dt.Compute(expression, null);
    // ...
}
```

#### Defensive Programming

- Check preconditions explicitly
- Don't assume valid state
- Validate before state changes
- Provide clear error messages for constraint violations

### Logging Architecture

**Current State**: No logging framework implemented

**Future Considerations** (not implemented):

- Structured logging with correlation IDs
- Log levels: Trace, Debug, Info, Warning, Error
- Separate logs for CLI and WPF applications
- Log to file or console in debug builds
- Consider Serilog or Microsoft.Extensions.Logging

### Error Recovery Strategies

#### No Automatic Recovery

The calculator does not attempt automatic error recovery:

- Invalid expressions are rejected immediately
- State (ANS, history) is preserved even on errors
- Users must correct their input and try again

#### State Preservation

Errors do not corrupt state:

```csharp
// ANS and history remain unchanged on error
var originalExpression = expression;
// ... validation and processing ...
// Only update state on success:
_lastResult = result;
_history.Add((originalExpression, result));
```

#### Test Support for State Reset

For testing only, state can be reset:

```csharp
// Test helper methods (not for production use)
internal static void ResetMemory() => _lastResult = null;
internal static void ResetHistory() => _history.Clear();
```

## Integration with Other Instructions

This error handling strategy integrates with:

- **Technology Stack**: Uses .NET exception types defined in project-instructions.md
- **Testing Strategy**: Error scenarios extensively covered in testing-instructions.md
- **Best Practices**: Defensive programming from practices-instructions.md
- **C# Patterns**: Exception handling details in csharp/csharp-style-instructions.md

## See Also

- **Project Architecture**: project-instructions.md (technology stack and data flow)
- **Best Practices**: practices-instructions.md (code quality and security)
- **Testing Strategy**: testing-instructions.md (error condition testing)
- **C# Exception Handling**: csharp/csharp-style-instructions.md (language-specific patterns)
- **WPF Error Display**: wpf/wpf-instructions.md (UI-specific error handling)

**Note**: This file defines the overall error handling architecture and exception usage patterns. For language-specific exception handling syntax, exception filter patterns, and detailed implementation examples, refer to the appropriate technology-specific instruction files.
