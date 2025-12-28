---
applyTo: "**"
triggers:
  - "best practices"
  - "performance guidelines"
  - "security practices"
  - "code quality"
  - "development workflow"
---

# Development Best Practices

## AI Assistant Guidelines

- **CRITICAL**: Use .NET 8.0 SDK ONLY - do not suggest .NET 9.0 or other versions
- Always suggest async/await patterns for I/O operations (not applicable to current calculator - synchronous by design)
- Include comprehensive error handling and logging in all code suggestions (see error-handling-instructions.md)
- Recommend appropriate security practices for desktop applications
- Reference specific testing coverage standards (80%+ for critical business logic as defined in testing-instructions.md)
- Focus on cross-technology best practices applicable to both CLI and WPF
- Consider performance implications for expression evaluation and UI responsiveness

## Cross-Technology Best Practices

### Code Quality Standards

#### Static Analysis and Warnings

- **Nullable Reference Types**: Enabled across all projects (`<Nullable>enable</Nullable>`)
- **Implicit Usings**: Enabled to reduce boilerplate (`<ImplicitUsings>enable</ImplicitUsings>`)
- **Treat Warnings as Errors**: Recommended for production builds
- **Code Analysis**: Use built-in .NET analyzers

#### Code Organization

- **Single Responsibility**: Each class should have one clear purpose
  - `Evaluator.cs`: Expression evaluation only
  - `Program.cs`: CLI interface and REPL only
  - `MainWindow.xaml.cs`: WPF UI interactions only
- **Static vs Instance**: Use static classes for stateless utilities; use instance classes for UI components
- **File-Scoped Namespaces**: Prefer modern C# file-scoped namespace declarations

#### Maintainability

- **Clear Naming**: Use descriptive, self-documenting names
- **XML Documentation**: Provide XML doc comments for public APIs (see `Evaluator.cs` as reference)
- **Avoid Magic Numbers**: Use named constants for numeric literals
- **Keep Methods Short**: Prefer methods under 50 lines; extract complex logic into helper methods

### Performance Guidelines

#### Expression Evaluation Performance

- **Minimize String Allocations**: Use `StringBuilder` for complex string manipulations
- **Cache Compiled Expressions**: Current implementation re-parses each time (acceptable for educational project)
- **Avoid Repeated Regex**: Use static readonly `Regex` instances for frequently used patterns
- **Function Evaluation**: Recursive function replacement is acceptable for small expressions

#### UI Performance

- **Avoid Blocking UI Thread**: Keep button click handlers fast (< 100ms)
- **Debounce User Input**: Not required for current simple calculator
- **Efficient Collection Updates**: Use `ObservableCollection<T>` for data-bound lists in WPF

#### Memory Management

- **Dispose Resources**: Use `using` statements for IDisposable resources
- **Avoid Memory Leaks**: Be cautious with event handlers in WPF (use weak references if needed)
- **History Bounds**: Consider limiting calculation history size for long-running sessions

### Security Best Practices

#### Input Validation

- **Validate All User Input**: Check for empty, null, and malformed expressions
- **Whitelist Characters**: Reject invalid characters early (alphabetic chars except ANS and functions)
- **Parentheses Matching**: Validate balanced parentheses before evaluation
- **Expression Length Limits**: Consider maximum expression length to prevent abuse

#### Error Information Disclosure

- **User-Friendly Messages**: Don't expose internal implementation details in error messages
- **Log Detailed Errors**: Log full exception details for debugging (not implemented in current version)
- **Avoid Stack Traces in UI**: Display sanitized error messages to users

#### Desktop Application Security

- **No Arbitrary Code Execution**: Use safe expression evaluators (DataTable.Compute is safe)
- **Local Storage**: If persisting history, use secure local storage APIs
- **No Network Communication**: Current calculator is offline-only (no security concerns)

### Development Workflow Best Practices

#### Version Control

- **Atomic Commits**: Each commit should represent a single logical change
- **Descriptive Messages**: Use clear commit messages explaining "why" not just "what"
- **Branch Strategy**: Use feature branches for new development
- **Mob Programming**: Use `mob.sh` for collaborative development with regular rotations

#### Continuous Integration

- **Build on Every Commit**: CI pipeline runs on all pushes and PRs
- **Automated Testing**: All tests run automatically in CI (see `.github/workflows/ci.yml`)
- **Coverage Enforcement**: Minimum 50% coverage enforced in CI
- **Fast Feedback**: Keep CI pipeline under 5 minutes

#### Test-Driven Development

- **Red-Green-Refactor**: Write failing test → make it pass → refactor
- **Test First**: Write tests before implementation code
- **Incremental Development**: Small, testable increments
- **Continuous Refactoring**: Improve code structure while maintaining passing tests

## Technology Integration Patterns

### CLI and WPF Sharing Core Logic

**Pattern**: Both UI layers depend on the same core library (`calculator/Evaluator.cs`)

**Best Practices**:

- Keep UI-agnostic logic in core library
- UI layers handle presentation and user interaction only
- No business logic in `Program.cs` or `MainWindow.xaml.cs`
- Error handling delegated to UI layer

**Example**:

```csharp
// Good: Core library is UI-agnostic
public static class Evaluator
{
    public static decimal Evaluate(string expression) { /* ... */ }
}

// Good: UI handles presentation
// CLI
var result = Evaluator.Evaluate(expression);
Console.WriteLine(result.ToString(CultureInfo.InvariantCulture));

// WPF
var result = Evaluator.Evaluate(expression);
ResultTextBlock.Text = result.ToString();
```

### Configuration Management

**Build Configurations**: Debug and Release

- Debug: Includes symbols, detailed error information
- Release: Optimized, minimal symbols

**Project References**: SDK-style project references (no version numbers needed)

```xml
<ProjectReference Include="..\calculator\Calculator.csproj" />
```

**Technology Versions**: Reference project-instructions.md for authoritative version information

### Dependency Management

**NuGet Packages**: Centralized in project files

- Test dependencies in `Calculator.Tests.csproj` only
- No external runtime dependencies for core calculator
- Keep dependencies minimal and well-justified

**Framework Dependencies**:

- `System.Data`: Built-in for DataTable.Compute
- `System.Math`: Built-in for mathematical functions
- No third-party expression evaluators

## See Also

- **Technology Stack**: project-instructions.md (authoritative version information)
- **Error Handling**: error-handling-instructions.md (detailed error handling implementation)
- **Testing Standards**: testing-instructions.md (specific coverage targets and strategies)
- **Code Organization**: style-instructions.md (project-level file organization)
- **C# Best Practices**: csharp/csharp-style-instructions.md (detailed coding standards)
- **WPF Best Practices**: wpf/wpf-instructions.md (UI-specific patterns)

**Note**: This file focuses on high-level, cross-technology best practices. For detailed coding standards, naming conventions, and framework-specific patterns, refer to the appropriate technology-specific instruction files.
