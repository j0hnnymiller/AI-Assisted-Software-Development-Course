# Issue 009: Fix Potential Infinite Loop in ReplaceFunctions

## Description

The `ReplaceFunctions` method in `Evaluator.cs` contains a logic path that could potentially result in an infinite loop if the identifier parsing and advancement logic fails.

## Current Behavior

In `Evaluator.cs`, lines 78-97:

```csharp
while (i < expression.Length)
{
    // find a letter start
    if (!char.IsLetter(expression[i]))
    {
        i++;
        continue;
    }

    // read the identifier
    int startIdent = i;
    while (i < expression.Length && char.IsLetter(expression[i])) i++;
    var ident = expression.Substring(startIdent, i - startIdent);

    // skip whitespace
    int j = i;
    while (j < expression.Length && char.IsWhiteSpace(expression[j])) j++;

    if (j >= expression.Length || expression[j] != '(')
    {
        // not a function call; move on from startIdent+1 to avoid infinite loop
        i = startIdent + 1;
        continue;
    }
```

## Problem

After processing an identifier that is not a supported function (line 111-115):

```csharp
var matched = Array.Find(functions, f => string.Equals(f, ident, StringComparison.OrdinalIgnoreCase));
if (matched == null)
{
    // unknown identifier; leave as-is (will be rejected later)
    i = startIdent + 1;
    continue;
}
```

The code sets `i = startIdent + 1` and continues. However, there's a subtle issue:

- If the unknown identifier is followed by `(`, we enter the conditional at line 95
- Then at line 111, we find no match and set `i = startIdent + 1`
- On the next iteration, we might read the same identifier again if the expression is malformed

While this _usually_ works, certain edge cases with unusual whitespace or character combinations could potentially cause issues.

## Potential Edge Cases

1. Expression with consecutive unknown function calls: `unknownA(unknownB(5))`
2. Malformed expressions with unusual whitespace patterns
3. Mixed valid and invalid identifiers

## Impact

- **Severity**: Medium
- **Likelihood**: Low (requires specific malformed input)
- **Effect**: Infinite loop, application hang

## Proposed Solution

### Option 1: Improve Loop Invariant

Ensure that `i` always advances by adding an assertion or check:

```csharp
if (matched == null)
{
    // unknown identifier; leave as-is (will be rejected later)
    int nextI = startIdent + 1;
    if (nextI <= i)  // Safety check
        throw new InvalidOperationException("Internal error: infinite loop prevention");
    i = nextI;
    continue;
}
```

### Option 2: Refactor Parsing Logic

Use a more robust state machine or parser that guarantees forward progress:

- Tokenize the expression first
- Process tokens sequentially
- Guarantee each token is consumed exactly once

### Option 3: Add Iteration Limit

Add a safeguard against infinite loops:

```csharp
int iterations = 0;
int maxIterations = expression.Length * 2;  // Conservative upper bound

while (i < expression.Length)
{
    if (++iterations > maxIterations)
        throw new InvalidOperationException("Expression parsing exceeded maximum iterations");

    // ... existing logic
}
```

## Recommended Approach

Combine **Option 1** (immediate safety check) with **Option 2** (refactor for clarity):

1. **Short-term**: Add safety checks to prevent infinite loops
2. **Long-term**: Refactor to a cleaner tokenization-based parser (see Issue #003)

## Files Affected

- `calculator/Evaluator.cs` (lines 72-172)

## Test Coverage

Add tests for edge cases:

```csharp
[TestMethod]
public void ReplaceFunctions_UnknownFunctionFollowedByKnown()
{
    // Should reject unknown but process known
    var ex = Assert.ThrowsException<ArgumentException>(() =>
        Evaluator.Evaluate("unknownFunc(sqrt(9))"));
    Assert.IsTrue(ex.Message.Contains("invalid characters"));
}

[TestMethod]
public void ReplaceFunctions_ConsecutiveUnknownFunctions()
{
    // Should not hang, should throw ArgumentException
    var ex = Assert.ThrowsException<ArgumentException>(() =>
        Evaluator.Evaluate("unknownA(unknownB(5))"));
    Assert.IsTrue(ex.Message.Contains("invalid characters"));
}

[TestMethod]
public void ReplaceFunctions_ComplexNesting()
{
    // Should handle deep nesting without hanging
    var result = Evaluator.Evaluate("sqrt(sqrt(sqrt(256)))");
    Assert.AreEqual(2m, result);
}
```

## Related Issues

- Issue #003: Refactor evaluator to separate parser and execution
- Issue #007: Fix recursive evaluate causing history pollution

## Priority

Medium - Low likelihood but high impact if triggered. Should be addressed during parser refactoring.
