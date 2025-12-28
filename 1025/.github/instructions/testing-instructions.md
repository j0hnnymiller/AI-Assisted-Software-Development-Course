---
applyTo: "**"
triggers:
  - "testing strategy"
  - "test architecture"
  - "test coverage"
  - "quality gates"
  - "test organization"
---

# Testing Strategy and Architecture

## AI Assistant Guidelines

- **CRITICAL**: Use .NET 8.0 SDK ONLY - do not suggest .NET 9.0 or other versions
- Follow established testing pyramid principles: unit tests > integration tests > end-to-end tests
- Implement proper test isolation using `[TestInitialize]` to reset static state
- Focus on both positive and negative test scenarios
- Test edge cases: empty input, overflow, invalid characters, ANS before calculation
- **Use consistent coverage standards**: 80%+ for critical business logic (authoritative standard)
- Current target: 50% minimum (CI enforced), 77.72% achieved
- Write tests that document expected behavior through clear naming and assertions

## Cross-Technology Testing Architecture

### Testing Pyramid Implementation

```
        /\
       /  \      E2E Tests: Manual testing of full UI workflows
      /    \     (Not automated in current version)
     /------\
    / Integ. \   Integration Tests: CLI and WPF integration with Evaluator
   /----------\  (ProgramTests.cs, MainWindowTests.cs)
  /   Unit Tests \ Unit Tests: Core Evaluator logic
 /----------------\ (UnitTest1.cs, MemoryTests.cs, LogTests.cs, LogCommandTests.cs)
```

#### Coverage Standards (Authoritative)

**Enforced Standards**:

- **Minimum Coverage**: 50% (enforced in CI/CD pipeline)
- **Current Achievement**: 77.72% line coverage, 70.27% branch coverage
- **Recommended Target**: 80%+ for critical business logic

**Coverage by Component**:

- `Evaluator.cs`: Critical business logic - aim for 90%+
- `Program.cs`: CLI interface - 70%+ acceptable
- `MainWindow.xaml.cs`: WPF interface - 70%+ acceptable

**Uncovered Scenarios** (see `test-gap-analysis-report.md`):

- Some complex error conditions
- Rare edge cases in function evaluation
- UI interaction sequences

### Testing Framework Coordination

**MSTest Framework** (version 2.2.10):

- `[TestClass]` - Marks test classes
- `[TestMethod]` - Marks individual test methods
- `[TestInitialize]` - Setup method run before each test
- `[TestCleanup]` - Teardown method run after each test
- `Assert.*` - Assertion methods

**Coverage Collection**:

- Tool: coverlet.collector 3.2.0
- Format: Cobertura XML
- Report Generator: ReportGenerator (in CI pipeline)
- Output: HTML coverage reports in `coverage-report/`

### Test Data Management Strategy

**Current Approach**: Inline test data in test methods

**Examples**:

```csharp
[TestMethod]
public void TestAddition()
{
    var result = CalculatorLib.Evaluator.Evaluate("1+1");
    Assert.AreEqual(2m, result);
}
```

**For Multiple Inputs**: Use `DataRow` attribute (MSTest)

```csharp
[DataTestMethod]
[DataRow("1+1", 2)]
[DataRow("2+2*3", 8)]
[DataRow("10-5", 5)]
public void TestArithmetic(string expression, decimal expected)
{
    var result = Evaluator.Evaluate(expression);
    Assert.AreEqual(expected, result);
}
```

**State Management**:

- Reset `Evaluator` static state before each test using `[TestInitialize]`
- Use `ResetMemory()` and `ResetHistory()` helper methods

### Test Environment Management

**Consistent Environment**:

- Target Framework: net8.0-windows (to support WPF tests)
- Culture: InvariantCulture for numeric comparisons
- State: Reset before each test via `[TestInitialize]`

**Test Isolation**:

```csharp
[TestInitialize]
public void Setup()
{
    // Reset Evaluator state before each test
    CalculatorLib.Evaluator.ResetMemory();
    CalculatorLib.Evaluator.ResetHistory();
}
```

**CI/CD Environment** (GitHub Actions):

- OS: Windows (latest)
- .NET SDK: 8.0
- Build Configuration: Release (for performance)
- Coverage Collection: Enabled with Cobertura format

## Integration Testing Patterns

### CLI Integration Testing

**Pattern**: Test `Program.Main` and `Program.EvaluateAndPrint` with captured console output

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
    }
}
```

**Key Points**:

- Capture `Console.Out` and `Console.Error` using `StringWriter`
- Verify exit codes (return values)
- Verify correct output streams
- Reset console streams after test

### WPF Integration Testing

**Pattern**: Create WPF window instances in test, interact with controls, verify results

```csharp
[TestMethod]
public void TestMainWindow_ClickEvaluateButton()
{
    var window = new MainWindow();
    window.Show();

    window.ExpressionTextBox.Text = "2+2";
    // Simulate button click
    window.EvaluateButton.RaiseEvent(
        new RoutedEventArgs(Button.ClickEvent));

    Assert.AreEqual("4", window.ResultTextBlock.Text);
    window.Close();
}
```

**Challenges**:

- Requires `[STAThread]` for WPF tests
- May need `Dispatcher` for async operations
- UI automation is complex (current tests are basic)

**Current WPF Testing Approach**:

- Test basic control interactions
- Verify data binding updates
- Test history list population
- Manual testing for full UI workflows

### Core Library Unit Testing

**Pattern**: Direct testing of `Evaluator` class methods

```csharp
[TestMethod]
public void TestSqrtFunction()
{
    var result = Evaluator.Evaluate("sqrt(16)");
    Assert.AreEqual(4m, result);
}

[TestMethod]
public void TestANSMemory()
{
    Evaluator.Evaluate("5");
    var result = Evaluator.Evaluate("ANS*2");
    Assert.AreEqual(10m, result);
}

[TestMethod]
public void TestInvalidCharacters()
{
    Assert.ThrowsException<ArgumentException>(
        () => Evaluator.Evaluate("2+abc"));
}
```

**Coverage Focus**:

- All arithmetic operations
- All mathematical functions
- ANS memory behavior
- History tracking
- Error conditions
- Edge cases

## Testing Strategy Integration

This testing strategy coordinates with:

- **Technology Stack**: Uses MSTest 2.2.10 and coverlet defined in project-instructions.md
- **Error Handling**: Tests error scenarios defined in error-handling-instructions.md
- **Best Practices**: Implements TDD workflow from practices-instructions.md
- **C# Patterns**: Uses C# testing conventions from csharp/csharp-testing-instructions.md

## Quality Gates and Standards

### Definition of Done

A feature is complete when:

1. ✅ **All tests pass**: No failing tests in the test suite
2. ✅ **Coverage maintained**: Overall coverage ≥ 50% (CI enforced)
3. ✅ **New code tested**: New functionality has corresponding tests
4. ✅ **Edge cases covered**: Positive, negative, and boundary scenarios tested
5. ✅ **CI pipeline green**: All CI checks pass

### Test Review Standards

**Code Review Checklist for Tests**:

- [ ] Test names clearly describe what is being tested
- [ ] Tests are independent and can run in any order
- [ ] Tests use `[TestInitialize]` to reset state if needed
- [ ] Assertions are clear and specific
- [ ] Expected exceptions use `Assert.ThrowsException<T>`
- [ ] Numeric comparisons account for floating-point precision
- [ ] Tests are not overly complex (prefer simple, focused tests)

**Test Naming Convention**: See `csharp/csharp-testing-instructions.md`

### Continuous Quality Monitoring

**CI Pipeline** (`.github/workflows/ci.yml`):

1. Restore dependencies (`dotnet restore`)
2. Build solution (`dotnet build --no-restore`)
3. Run tests with coverage (`dotnet test --collect:"XPlat Code Coverage"`)
4. Generate HTML coverage report (ReportGenerator)
5. Fail build if coverage < 50%

**Coverage Reporting**:

- Cobertura XML format for machine processing
- HTML reports for human review
- Coverage trends tracked over time (via CI history)

**Quality Metrics**:

- **Line Coverage**: 77.72% (target: 80%+)
- **Branch Coverage**: 70.27% (target: 75%+)
- **Test Count**: 34 tests (all passing)
- **Test Execution Time**: < 1 second (fast feedback)

## Test Organization

### Test Project Structure

```
calculator.tests/
├── Calculator.Tests.csproj      # Test project file
├── UnitTest1.cs                 # Core Evaluator tests (arithmetic, functions)
├── MemoryTests.cs               # ANS memory tests
├── LogTests.cs                  # History logging tests
├── LogCommandTests.cs           # Log command tests
├── ProgramTests.cs              # CLI integration tests
└── MainWindowTests.cs           # WPF integration tests
```

### Test File Organization Principles

**By Feature**:

- One test file per major feature area
- `UnitTest1.cs`: Core arithmetic and function evaluation
- `MemoryTests.cs`: ANS memory behavior
- `LogTests.cs`: History tracking
- `LogCommandTests.cs`: REPL log command

**By Component**:

- `ProgramTests.cs`: Tests for CLI Program class
- `MainWindowTests.cs`: Tests for WPF MainWindow

**Naming**: See `csharp/csharp-testing-instructions.md` for detailed test naming conventions

## See Also

- **Project Architecture**: project-instructions.md (technology stack and testing framework versions)
- **Best Practices**: practices-instructions.md (TDD workflow and quality standards)
- **Error Handling**: error-handling-instructions.md (error scenario testing)
- **C# Testing Details**: csharp/csharp-testing-instructions.md (MSTest patterns and conventions)
- **Test Gap Analysis**: ../../test-gap-analysis-report.md (current coverage gaps)

**Note**: This file defines the overall testing strategy and cross-technology testing patterns. For specific testing frameworks, unit testing patterns, assertion strategies, and detailed MSTest implementation guidelines, refer to `csharp/csharp-testing-instructions.md`.
