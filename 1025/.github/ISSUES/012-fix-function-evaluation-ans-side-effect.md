# Issue 012: Function Evaluation Overwrites ANS (Side Effect)

## Description

When `ReplaceFunctions` recursively calls `Evaluate()` to process function arguments, it overwrites the `_lastResult` (ANS) with intermediate values. This means ANS doesn't correctly reflect the last _user-entered_ expression, but rather the last internal calculation.

## Current Behavior

Example scenario:

```csharp
Evaluator.Evaluate("10");      // ANS = 10
Evaluator.Evaluate("sqrt(25)"); // Internally evaluates "25", sets ANS = 25, then ANS = 5
// At this point, ANS = 5 (final result), not 25 (intermediate)
```

However, if user then types:

```csharp
Evaluator.Evaluate("ANS");     // Returns 5 (last result)
```

The problem is more subtle:

```csharp
Evaluator.Evaluate("100");         // ANS = 100
Evaluator.Evaluate("sqrt(4) + 1"); // Internally: Evaluate("4") sets ANS=4,
                                    // then final result 3, sets ANS=3
Evaluator.Evaluate("ANS");         // Returns 3 (correct final result)
```

But what if user wants to reuse ANS _during_ function evaluation?

```csharp
Evaluator.Evaluate("100");              // ANS = 100
Evaluator.Evaluate("sqrt(ANS/4) + 1");  // ANS should be 100, not updated mid-calculation
```

During evaluation of `sqrt(ANS/4)`:

1. ANS gets replaced with 100 → "sqrt(100/4)"
2. `ReplaceFunctions` calls `Evaluate("100/4")`
3. This sets ANS = 25 (WRONG! ANS should stay 100 until the full expression completes)
4. Final result = 6, ANS = 6

## Problem Statement

**ANS should only be updated after the complete top-level expression is evaluated, not during intermediate function argument evaluations.**

## Impact

- **Severity**: High
- **Correctness**: ANS can have unexpected values during nested evaluations
- **User Experience**: Breaks expected behavior of ANS referring to previous result

## Example of Bug

```csharp
[TestMethod]
public void Bug_FunctionEvaluation_OverwritesANS()
{
    Evaluator.ResetMemory();

    Evaluator.Evaluate("100");              // ANS = 100
    var result = Evaluator.Evaluate("sqrt(ANS/4) + 1");

    // Expected: sqrt(100/4) + 1 = sqrt(25) + 1 = 6
    Assert.AreEqual(6m, result);  // This passes

    // But ANS should still be 6 (result of the full expression)
    var ansCheck = Evaluator.Evaluate("ANS");
    Assert.AreEqual(6m, ansCheck);  // This might fail depending on implementation

    // The real issue: during evaluation, ANS got overwritten with 25
    // If we had tried to use ANS in a subsequent operation immediately,
    // it might have been 25 instead of 6
}
```

## Root Cause

Related to **Issue #007** (Recursive Evaluate Causing History Pollution).

In `Evaluator.cs`, line 118:

```csharp
var innerResultDecimal = Evaluate(inner);
```

This recursive call updates `_lastResult`:

```csharp
// Line 66
_lastResult = result;
```

Every recursive call to `Evaluate` overwrites ANS, even for intermediate calculations.

## Proposed Solution

Same as Issue #007: Extract evaluation logic into a private method that doesn't update state.

```csharp
public static decimal Evaluate(string expression)
{
    var originalExpression = expression;
    var result = EvaluateInternal(expression);

    // Only update memory and history at the top level
    _lastResult = result;
    _history.Add((originalExpression, result));

    return result;
}

private static decimal EvaluateInternal(string expression)
{
    // Core evaluation logic without side effects
    expression = ReplaceFunctions(expression);
    expression = ReplaceANS(expression);

    // ... rest of evaluation
    // Return result without updating _lastResult or _history
}

private static string ReplaceFunctions(string expression)
{
    // ... existing logic, but call EvaluateInternal instead of Evaluate
    var innerResultDecimal = EvaluateInternal(inner);  // No side effects
    // ...
}
```

## Alternative Solution: Track Evaluation Depth

```csharp
[ThreadStatic]
private static int _evaluationDepth = 0;

public static decimal Evaluate(string expression)
{
    _evaluationDepth++;
    try
    {
        var originalExpression = expression;
        // ... evaluation logic ...

        // Only update state at top level (depth == 1)
        if (_evaluationDepth == 1)
        {
            _lastResult = result;
            _history.Add((originalExpression, result));
        }

        return result;
    }
    finally
    {
        _evaluationDepth--;
    }
}
```

## Recommended Approach

The **private method** approach is cleaner and more maintainable than tracking depth.

## Files Affected

- `calculator/Evaluator.cs`

## Test Coverage

```csharp
[TestMethod]
public void EvaluateFunction_DoesNotOverwriteANS_UntilComplete()
{
    Evaluator.ResetMemory();
    Evaluator.Evaluate("100");

    var result = Evaluator.Evaluate("sqrt(ANS) + 1");
    Assert.AreEqual(11m, result);  // sqrt(100) + 1 = 11

    // Verify ANS is the final result, not intermediate
    var ansAfter = Evaluator.Evaluate("ANS");
    Assert.AreEqual(11m, ansAfter);
}

[TestMethod]
public void NestedFunctions_DoNotPolluteANS()
{
    Evaluator.ResetMemory();
    Evaluator.Evaluate("64");  // ANS = 64

    // sqrt(sqrt(ANS)) = sqrt(sqrt(64)) = sqrt(8) = 2.828...
    var result = Evaluator.Evaluate("sqrt(sqrt(ANS))");
    var expected = Math.Sqrt(Math.Sqrt(64));
    Assert.AreEqual((decimal)expected, result, 0.01m);

    // ANS should be the final result
    var ansCheck = Evaluator.Evaluate("ANS");
    Assert.AreEqual(result, ansCheck);
}
```

## Priority

High - This is a correctness bug that affects core functionality (ANS behavior)

## Related Issues

- Issue #007: Fix recursive evaluate causing history pollution (same root cause)
- Issue #008: Fix static state thread safety (related to state management)

## Fix Strategy

Since this shares the same root cause as Issue #007, they should be fixed together:

1. Refactor to separate internal evaluation from state-updating public API
2. Fix both history pollution and ANS overwriting in one change
3. Add comprehensive tests for both issues
