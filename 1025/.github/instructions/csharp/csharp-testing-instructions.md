---
applyTo: "**/*.cs"
triggers:
  - "C# testing"
  - "MSTest"
  - "test naming"
  - "test patterns"
  - "unit testing C#"
  - "test assertions"
---

# C# Testing Patterns and Conventions

## AI Assistant Guidelines

- **CRITICAL**: Use .NET 8.0 SDK ONLY - do not suggest .NET 9.0 or other versions
- Use MSTest 2.2.10 framework for all tests
- Follow AAA pattern: Arrange-Act-Assert
- Use descriptive test method names that explain the scenario
- Reset static state in `[TestInitialize]` methods
- Use `Assert.ThrowsException<T>` for exception testing
- Prefer `Assert.AreEqual` over `Assert.IsTrue` for comparisons
- Consider floating-point precision in numeric comparisons
- Isolate tests: each test should be independent and repeatable

## MSTest Framework Patterns

### Test Class Structure

**Attributes**:

- `[TestClass]`: Marks a class containing tests
- `[TestMethod]`: Marks an individual test method
- `[TestInitialize]`: Setup method run before each test
- `[TestCleanup]`: Teardown method run after each test (rarely needed)
- `[DataTestMethod]`: Marks a test method that accepts parameters
- `[DataRow(...)]`: Provides test data for data-driven tests

**Example**:

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace calculator.tests
{
    [TestClass]
    public class UnitTest1
    {
        [TestInitialize]
        public void Setup()
        {
            // Reset state before each test
            CalculatorLib.Evaluator.ResetMemory();
            CalculatorLib.Evaluator.ResetHistory();
        }

        [TestMethod]
        public void TestAddition()
        {
            // Arrange
            var expression = "1+1";

            // Act
            var result = CalculatorLib.Evaluator.Evaluate(expression);

            // Assert
            Assert.AreEqual(2m, result);
        }
    }
}
```

### Test Naming Conventions

**Format**: `Test<MethodName>_<Scenario>_<ExpectedBehavior>` or `Test<Scenario>`

**Examples**:

```csharp
✅ [TestMethod]
   public void TestAddition() { }

✅ [TestMethod]
   public void TestSubtraction() { }

✅ [TestMethod]
   public void TestDivisionByZero() { }

✅ [TestMethod]
   public void TestInvalidInput() { }

✅ [TestMethod]
   public void Evaluator_Using_ANS_Before_Any_Value_Throws() { }

✅ [TestMethod]
   public void Evaluator_Stores_Last_Result_In_ANS() { }

✅ [TestMethod]
   public void TestEvaluateAndPrint_ValidExpression() { }

✅ [TestMethod]
   public void TestMainWindow_ClickEvaluateButton() { }
```

**Naming Patterns**:

1. **Simple Feature Tests**: `Test<Feature>` (e.g., `TestAddition`, `TestSqrtFunction`)
2. **Edge Case Tests**: `Test<EdgeCase>` (e.g., `TestDivisionByZero`, `TestLargeNumberOverflow`)
3. **Method-Scenario-Outcome**: `<MethodUnderTest>_<Scenario>_<ExpectedOutcome>` (e.g., `Evaluator_Using_ANS_Before_Any_Value_Throws`)
4. **Integration Tests**: `Test<Component>_<Action>` (e.g., `TestMainWindow_ClickEvaluateButton`)

**Rules**:

- Clear, descriptive names that explain what is being tested
- No need to include "Test" in scenarios if using `Test` prefix
- Use underscores for readability in complex scenario names
- Be consistent within each test file

## Arrange-Act-Assert (AAA) Pattern

### Standard AAA Structure

```csharp
[TestMethod]
public void TestPrecedenceAndFraction()
{
    // Arrange: Set up test data and preconditions
    var expression = "2+2*3";

    // Act: Execute the method under test
    var result = CalculatorLib.Evaluator.Evaluate(expression);

    // Assert: Verify the result
    Assert.AreEqual(8m, result);
}
```

### One-Liner Tests (When Appropriate)

```csharp
[TestMethod]
public void TestAddition()
{
    // Arrange-Act-Assert combined for simple tests
    var result = CalculatorLib.Evaluator.Evaluate("1+1");
    Assert.AreEqual(2m, result);
}
```

### Complex Setup

```csharp
[TestMethod]
public void TestANSMemory()
{
    // Arrange: Set up initial state
    CalculatorLib.Evaluator.Evaluate("5");

    // Act: Use ANS in next calculation
    var result = CalculatorLib.Evaluator.Evaluate("ANS*2");

    // Assert: Verify result
    Assert.AreEqual(10m, result);
}
```

## Assertion Patterns

### Equality Assertions

**Prefer `AreEqual` for Comparisons**:

```csharp
✅ Assert.AreEqual(2m, result);
✅ Assert.AreEqual(expected, actual);
✅ Assert.AreNotEqual(0m, result);
```

**Avoid `IsTrue` for Equality**:

```csharp
❌ Assert.IsTrue(result == 2m);  // Less clear
✅ Assert.AreEqual(2m, result);  // Better
```

### Floating-Point Comparisons

**Problem**: Direct equality fails for floating-point precision

```csharp
❌ Assert.AreEqual(3.33333333333333m, result);  // May fail
```

**Solution**: Use tolerance-based comparison

```csharp
✅ Assert.IsTrue(Math.Abs((double)(result - 3.33333333333333m)) < 1e-9);

✅ // Or use delta parameter (if supported by assertion)
   Assert.AreEqual(3.33333333333333m, result, 0.000001m);
```

**Example from Codebase**:

```csharp
[TestMethod]
public void TestPrecedenceAndFraction()
{
    var frac = CalculatorLib.Evaluator.Evaluate("10/3");
    // Approximate check for floating-point result
    Assert.IsTrue(Math.Abs((double)(frac - 3.33333333333333m)) < 1e-9);
}
```

### Exception Assertions

**Use `Assert.ThrowsException<T>`**:

```csharp
✅ [TestMethod]
   public void TestDivisionByZero()
   {
       Assert.ThrowsException<OverflowException>(
           () => CalculatorLib.Evaluator.Evaluate("5/(3-3)"));
   }

✅ [TestMethod]
   public void TestInvalidInput()
   {
       Assert.ThrowsException<ArgumentException>(
           () => CalculatorLib.Evaluator.Evaluate(string.Empty));
   }

✅ [TestMethod]
   public void Evaluator_Using_ANS_Before_Any_Value_Throws()
   {
       Assert.ThrowsException<InvalidOperationException>(
           () => CalculatorLib.Evaluator.Evaluate("ANS+1"));
   }
```

**Verify Exception Message** (Optional):

```csharp
✅ var ex = Assert.ThrowsException<ArgumentException>(
       () => Evaluator.Evaluate(""));
   Assert.AreEqual("Empty expression", ex.Message);
```

### Null Assertions

```csharp
✅ Assert.IsNull(obj);
✅ Assert.IsNotNull(obj);
```

### Boolean Assertions

```csharp
✅ Assert.IsTrue(condition);
✅ Assert.IsFalse(condition);
```

### Collection Assertions

```csharp
✅ Assert.AreEqual(3, list.Count);
✅ CollectionAssert.Contains(list, item);
✅ CollectionAssert.AreEqual(expected, actual);  // Order matters
✅ CollectionAssert.AreEquivalent(expected, actual);  // Order doesn't matter
```

## Test Isolation and State Management

### Static State Reset

**Problem**: `Evaluator` uses static fields for ANS and history
**Solution**: Reset state before each test

```csharp
[TestInitialize]
public void Setup()
{
    CalculatorLib.Evaluator.ResetMemory();
    CalculatorLib.Evaluator.ResetHistory();
}
```

**Example**:

```csharp
namespace calculator.tests
{
    [TestClass]
    public class MemoryTests
    {
        [TestInitialize]
        public void Setup()
        {
            CalculatorLib.Evaluator.ResetMemory();
            CalculatorLib.Evaluator.ResetHistory();
        }

        [TestMethod]
        public void Evaluator_Stores_Last_Result_In_ANS()
        {
            CalculatorLib.Evaluator.Evaluate("5");
            var result = CalculatorLib.Evaluator.Evaluate("ANS*2");
            Assert.AreEqual(10m, result);
        }
    }
}
```

### Console Output Capture

**For CLI Testing**: Capture `Console.Out` and `Console.Error`

```csharp
[TestMethod]
public void TestEvaluateAndPrint_ValidExpression()
{
    using (var swOut = new System.IO.StringWriter())
    using (var swErr = new System.IO.StringWriter())
    {
        Console.SetOut(swOut);
        Console.SetError(swErr);

        var success = Program.EvaluateAndPrint("2+2");

        Assert.IsTrue(success);
        Assert.AreEqual("4\r\n", swOut.ToString());
        Assert.AreEqual(string.Empty, swErr.ToString());

        // Restore console (optional if TestCleanup handles it)
        Console.SetOut(new System.IO.StreamWriter(Console.OpenStandardOutput()));
    }
}
```

**Cleanup** (if needed):

```csharp
[TestCleanup]
public void Cleanup()
{
    // Restore console streams
    var standardOut = new System.IO.StreamWriter(Console.OpenStandardOutput());
    standardOut.AutoFlush = true;
    Console.SetOut(standardOut);
}
```

## Data-Driven Testing

### Using DataTestMethod and DataRow

```csharp
[DataTestMethod]
[DataRow("1+1", 2)]
[DataRow("2+2*3", 8)]
[DataRow("10-5", 5)]
[DataRow("10/2", 5)]
public void TestArithmeticOperations(string expression, decimal expected)
{
    var result = CalculatorLib.Evaluator.Evaluate(expression);
    Assert.AreEqual(expected, result);
}

[DataTestMethod]
[DataRow("sqrt(16)", 4)]
[DataRow("sqrt(9)", 3)]
[DataRow("sqrt(2)", 1.41421356)]
public void TestSqrtFunction(string expression, double expected)
{
    var result = CalculatorLib.Evaluator.Evaluate(expression);
    Assert.AreEqual((decimal)expected, result, 0.0001m);
}
```

**Benefits**:

- Reduce code duplication
- Easy to add more test cases
- Clear test data visibility

**When to Use**:

- Multiple similar test cases
- Testing different input combinations
- Boundary value testing

**When Not to Use**:

- Complex setup/teardown per case
- Tests with different assertion logic
- When readability suffers

## WPF Testing Patterns

### Testing WPF Controls

**Threading Requirement**: WPF tests need `[STAThread]`

```csharp
[TestMethod]
public void TestMainWindow_ClickEvaluateButton()
{
    // Arrange: Create WPF window
    var window = new Calculator.Wpf.MainWindow();
    window.Show();

    // Act: Set input and trigger evaluation
    window.ExpressionTextBox.Text = "2+2";
    window.EvaluateButton.RaiseEvent(
        new System.Windows.RoutedEventArgs(
            System.Windows.Controls.Primitives.ButtonBase.ClickEvent));

    // Assert: Verify result
    Assert.AreEqual("4", window.ResultTextBlock.Text);

    // Cleanup
    window.Close();
}
```

**Challenges**:

- Requires dispatcher for async operations
- UI automation can be complex
- Tests may be flaky without proper synchronization

**Current Approach**: Basic control interaction testing; complex UI workflows tested manually

## Test Organization

### Test File Structure

**One Test Class Per File**:

```csharp
// UnitTest1.cs
[TestClass]
public class UnitTest1 { /* Core Evaluator tests */ }

// MemoryTests.cs
[TestClass]
public class MemoryTests { /* ANS memory tests */ }

// ProgramTests.cs
[TestClass]
public class ProgramTests { /* CLI integration tests */ }
```

### Test Method Grouping

**Within a Test Class**: Group by feature or scenario

```csharp
[TestClass]
public class UnitTest1
{
    // Arithmetic operations
    [TestMethod] public void TestAddition() { }
    [TestMethod] public void TestSubtraction() { }
    [TestMethod] public void TestMultiplication() { }

    // Functions
    [TestMethod] public void TestSqrtFunction() { }
    [TestMethod] public void TestSinFunction() { }

    // Error cases
    [TestMethod] public void TestDivisionByZero() { }
    [TestMethod] public void TestInvalidInput() { }
}
```

## Code Coverage Considerations

### Target Framework

**Important**: Tests must target `net8.0-windows` to support WPF tests

```xml
<TargetFramework>net8.0-windows</TargetFramework>
```

### Coverage Collection

**Command**: `dotnet test --collect:"XPlat Code Coverage"`

**Output**: Cobertura XML format in `TestResults/` directory

### Coverage Interpretation

**Line Coverage**: Percentage of code lines executed during tests
**Branch Coverage**: Percentage of decision branches tested

**Current Project**:

- Line Coverage: 77.72%
- Branch Coverage: 70.27%
- Target: 80%+ for critical paths, 50% minimum enforced

## Best Practices Summary

### Do's ✅

- ✅ Use `[TestInitialize]` to reset static state
- ✅ Write descriptive test method names
- ✅ Follow AAA pattern for clarity
- ✅ Use `Assert.AreEqual` for comparisons
- ✅ Use `Assert.ThrowsException<T>` for exception testing
- ✅ Consider floating-point precision in assertions
- ✅ Test both positive and negative scenarios
- ✅ Test edge cases (empty input, overflow, invalid characters)
- ✅ Keep tests simple and focused
- ✅ Make tests independent and repeatable

### Don'ts ❌

- ❌ Don't rely on test execution order
- ❌ Don't share state between tests without proper cleanup
- ❌ Don't use `Assert.IsTrue(x == y)` when `Assert.AreEqual(x, y)` is clearer
- ❌ Don't ignore floating-point precision issues
- ❌ Don't write overly complex test logic
- ❌ Don't test multiple unrelated things in one test method
- ❌ Don't forget to close WPF windows in tests

## See Also

- **Testing Strategy**: testing-instructions.md (overall testing approach and coverage standards)
- **C# Style**: csharp-style-instructions.md (general C# coding conventions)
- **Error Handling**: error-handling-instructions.md (exception types and patterns)
- **Project Structure**: project-instructions.md (test project configuration)

**Note**: This file provides detailed MSTest-specific patterns and C# testing conventions. For overall testing strategy, coverage targets, and cross-technology testing patterns, refer to `testing-instructions.md`.
