# Issue 011: Improve Division by Zero Error Handling

## Description

Division by zero currently throws an `OverflowException` which is not the most semantically appropriate exception type for this error. Additionally, the error message could be more user-friendly.

## Current Behavior

From `UnitTest1.cs`, lines 36-39:

```csharp
[TestMethod]
public void TestDivisionByZero()
{
    // Division by zero should cause an exception (overflow during conversion)
    Assert.ThrowsException<OverflowException>(() => CalculatorLib.Evaluator.Evaluate("5/(3-3)"));
}
```

When users divide by zero:

1. `DataTable.Compute` returns `double.PositiveInfinity`
2. Conversion to `decimal` throws `OverflowException`
3. User sees: "Error: Value was either too large or too small for a Decimal."

## Problems

1. **Misleading Exception Type**: `OverflowException` doesn't clearly indicate division by zero
2. **Unclear Error Message**: "Value was either too large or too small for a Decimal" is confusing for division by zero
3. **Inconsistent Handling**: Other math errors (like `NaN` from sqrt of negative) are caught, but not division by zero

## Expected Behavior

Users should see a clear, specific error message:

- "Error: Division by zero"
- Or: "Error: Cannot divide by zero"

## Impact

- **Severity**: Low-Medium
- **User Experience**: Confusing error messages
- **Debugging**: Harder to diagnose issues

## Root Cause

In `Evaluator.cs`, lines 47-58:

```csharp
var obj = dt.Compute(expression, null);
if (obj == null) throw new InvalidOperationException("Could not evaluate expression");

var result = obj switch
{
    double d => Convert.ToDecimal(d),
    decimal m => m,
    int i => i,
    long l => l,
    string s when decimal.TryParse(s, NumberStyles.Any, CultureInfo.InvariantCulture, out var x) => x,
    _ => Convert.ToDecimal(obj)
};
```

When division by zero occurs:

- `dt.Compute` returns `double.PositiveInfinity` or `double.NegativeInfinity`
- `Convert.ToDecimal(double.PositiveInfinity)` throws `OverflowException`

## Proposed Solution

### Option 1: Check for Infinity Before Conversion

```csharp
var result = obj switch
{
    double d when double.IsInfinity(d) =>
        throw new DivideByZeroException("Division by zero"),
    double d when double.IsNaN(d) =>
        throw new InvalidOperationException("Invalid mathematical operation"),
    double d => Convert.ToDecimal(d),
    decimal m => m,
    int i => i,
    long l => l,
    string s when decimal.TryParse(s, NumberStyles.Any, CultureInfo.InvariantCulture, out var x) => x,
    _ => Convert.ToDecimal(obj)
};
```

### Option 2: Catch and Rethrow with Better Message

```csharp
try
{
    var result = obj switch
    {
        double d => Convert.ToDecimal(d),
        // ... rest of cases
    };
}
catch (OverflowException ex)
{
    // Check if this was caused by infinity (division by zero)
    if (obj is double d && double.IsInfinity(d))
        throw new DivideByZeroException("Division by zero", ex);
    throw;  // Re-throw if it's a genuine overflow
}
```

## Recommended Approach

**Option 1** is cleaner and more explicit. It handles the issue at the source before attempting conversion.

### Additional Improvements

Also check for `NaN` (Not a Number) results from invalid operations:

```csharp
double d when double.IsInfinity(d) =>
    throw new DivideByZeroException("Division by zero"),
double d when double.IsNaN(d) =>
    throw new InvalidOperationException("Result is not a valid number"),
```

## Files Affected

- `calculator/Evaluator.cs` (lines 47-58)
- `calculator.tests/UnitTest1.cs` (update test to expect `DivideByZeroException`)

## Test Coverage

Update existing test:

```csharp
[TestMethod]
public void TestDivisionByZero()
{
    var ex = Assert.ThrowsException<DivideByZeroException>(() =>
        CalculatorLib.Evaluator.Evaluate("5/(3-3)"));
    Assert.AreEqual("Division by zero", ex.Message);
}
```

Add new tests:

```csharp
[TestMethod]
public void TestDivisionByZeroNegative()
{
    Assert.ThrowsException<DivideByZeroException>(() =>
        CalculatorLib.Evaluator.Evaluate("-10/0"));
}

[TestMethod]
public void TestInvalidMathOperation()
{
    // sqrt of negative number (if not already handled)
    Assert.ThrowsException<InvalidOperationException>(() =>
        CalculatorLib.Evaluator.Evaluate("sqrt(-1)"));
}
```

## Breaking Changes

⚠️ **Warning**: Changing the exception type from `OverflowException` to `DivideByZeroException` is a breaking change for any code that catches `OverflowException` specifically.

**Migration Strategy**:

1. Update the test to expect `DivideByZeroException`
2. Update documentation/README
3. Consider keeping `OverflowException` as inner exception for compatibility

## Priority

Low-Medium - Improves user experience and code clarity, but not blocking

## Related Issues

- Issue #004: Add tests for error mapping and user-friendly messages
