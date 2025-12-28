# Issue 007: Fix Recursive Evaluate Call Causing History Pollution

## Description

The `ReplaceFunctions` method in `Evaluator.cs` recursively calls `Evaluate()` to process nested function arguments. This causes intermediate evaluations to be added to the history log, polluting it with internal calculations that users should not see.

## Current Behavior

When evaluating an expression like `sqrt(9) + 5`:

1. `Evaluate("sqrt(9) + 5")` is called
2. `ReplaceFunctions` calls `Evaluate("9")` internally (line 118)
3. History gets polluted with entry: `"9" = 9`
4. History gets the intended entry: `"sqrt(9) + 5" = 8`

Users see both entries in history when they should only see the top-level expression.

## Expected Behavior

Only the top-level expression `"sqrt(9) + 5"` should appear in history. Internal evaluations for function arguments should not create history entries.

## Impact

- **Severity**: Medium
- **User Experience**: History becomes cluttered with internal calculations
- **Data Integrity**: History log contains misleading entries

## Root Cause

In `Evaluator.cs`, line 118:

```csharp
var innerResultDecimal = Evaluate(inner);
```

This recursive call to `Evaluate()` updates both `_lastResult` (ANS) and appends to `_history`, which should only happen for top-level user expressions.

## Proposed Solution

1. Extract the core evaluation logic into a private method that doesn't update history or memory
2. The public `Evaluate` method should call this private method and only update history/memory for top-level calls
3. `ReplaceFunctions` should call the private evaluation method to avoid history pollution

**Alternative**: Add a boolean parameter to indicate internal vs. user-initiated evaluations.

## Files Affected

- `calculator/Evaluator.cs`

## Test Coverage

Add tests to verify:

1. Function calls don't create intermediate history entries
2. Nested functions (e.g., `sqrt(sin(0))`) only create one history entry
3. ANS is still correctly updated for top-level expressions

## Related Issues

- None

## Priority

Medium - Affects user experience but doesn't break core functionality
