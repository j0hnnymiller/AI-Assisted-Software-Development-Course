# Issue 008: Fix Static State Thread Safety Issues

## Description

The `Evaluator` class uses static fields for `_lastResult` and `_history`, making it unsafe for concurrent use and preventing multiple independent calculator instances.

## Current Behavior

```csharp
private static decimal? _lastResult = null;
private static System.Collections.Generic.List<(string Expression, decimal Result)> _history = new();
```

All instances of calculators (CLI and WPF) share the same memory and history state.

## Problems

### 1. Thread Safety

If multiple threads evaluate expressions simultaneously (e.g., WPF UI thread and background worker):

- Race conditions on `_lastResult` and `_history`
- ANS could return unexpected values
- History entries could be interleaved or lost

### 2. Multiple Calculator Instances

Cannot create independent calculator instances with separate state:

- WPF and CLI calculators share the same ANS and history
- Running multiple calculator windows would interfere with each other
- Unit tests must reset state between tests (current workaround)

### 3. Test Isolation

Tests require explicit `ResetMemory()` and `ResetHistory()` calls to avoid state leakage between tests.

## Expected Behavior

The evaluator should either:

1. Be thread-safe for concurrent access, OR
2. Support instance-based state for independent calculators

## Impact

- **Severity**: High
- **Concurrency**: Not thread-safe
- **Testability**: Requires manual state reset
- **Architecture**: Limits design to singleton pattern

## Proposed Solutions

### Option A: Make Instance-Based (Recommended)

```csharp
public class Evaluator
{
    private decimal? _lastResult = null;
    private List<(string Expression, decimal Result)> _history = new();

    public decimal Evaluate(string expression) { ... }
    public IReadOnlyList<(string Expression, decimal Result)> GetHistory() => _history.AsReadOnly();
    public void ResetMemory() => _lastResult = null;
    public void ResetHistory() => _history.Clear();
}
```

**Pros**:

- Natural object-oriented design
- Thread-safe by design (each instance has own state)
- Supports multiple independent calculators
- Tests don't need manual state reset

**Cons**:

- Breaking change to public API
- Requires updating Program.cs and MainWindow.xaml.cs

### Option B: Add Thread Synchronization

Keep static methods but add locking:

```csharp
private static readonly object _lock = new object();
private static decimal? _lastResult = null;

public static decimal Evaluate(string expression)
{
    lock (_lock)
    {
        // ... evaluation logic
    }
}
```

**Pros**:

- No API changes
- Thread-safe

**Cons**:

- Serializes all evaluations (performance bottleneck)
- Still shares state globally
- Doesn't solve multiple calculator instance problem

## Recommended Approach

**Option A** (Instance-Based) is recommended because:

1. Better object-oriented design
2. Naturally thread-safe without locking overhead
3. Supports multiple calculator instances
4. Easier to test
5. More extensible for future features

## Migration Strategy

1. Make `Evaluator` instance-based
2. Update `Program.cs` to create and reuse an `Evaluator` instance
3. Update `MainWindow.xaml.cs` to create a private `Evaluator` field
4. Update all tests to create fresh `Evaluator` instances
5. Remove `ResetMemory()` and `ResetHistory()` internal methods (no longer needed)
6. Update README and documentation

## Files Affected

- `calculator/Evaluator.cs` (primary change)
- `calculator/Program.cs` (create instance)
- `calculator-wpf/MainWindow.xaml.cs` (create instance)
- All test files in `calculator.tests/`

## Test Coverage

Add tests to verify:

1. Multiple `Evaluator` instances maintain separate state
2. ANS and history don't leak between instances
3. Concurrent evaluations on different instances work correctly

## Priority

High - Architectural issue affecting scalability, testability, and correctness
