---
applyTo:
  - "**/*.xaml"
  - "**/*.xaml.cs"
triggers:
  - "WPF"
  - "XAML"
  - "Windows desktop"
  - "MainWindow"
  - "data binding"
  - "event handlers"
---

# WPF Development Patterns and Best Practices

## AI Assistant Guidelines

- **CRITICAL**: Use .NET 8.0 SDK ONLY - do not suggest .NET 9.0 or other versions
- Follow WPF naming conventions for controls (PascalCase with descriptive suffixes)
- Use XAML for UI layout; avoid programmatic UI creation
- Use code-behind for event handlers and UI logic
- Keep business logic in separate classes (e.g., `Evaluator.cs`)
- Use data binding for dynamic content where appropriate
- Follow event-driven programming patterns for user interactions
- Use proper resource management (close windows, dispose resources)

## WPF Project Structure

### File Organization

```
calculator-wpf/
├── Calculator.Wpf.csproj          # Project file
├── App.xaml                       # Application definition
├── App.xaml.cs                    # Application code-behind
├── MainWindow.xaml                # Main window UI layout
├── MainWindow.xaml.cs             # Main window code-behind
├── bin/                           # Build outputs
├── obj/                           # Intermediate files
└── README.md                      # WPF-specific documentation
```

### Project File Configuration

**Target Framework**: `net8.0-windows`
**SDK**: `Microsoft.NET.Sdk.WindowsDesktop`
**Settings**:

```xml
<Project Sdk="Microsoft.NET.Sdk.WindowsDesktop">
    <PropertyGroup>
        <TargetFramework>net8.0-windows</TargetFramework>
        <UseWPF>true</UseWPF>
        <OutputType>WinExe</OutputType>
        <ImplicitUsings>enable</ImplicitUsings>
        <Nullable>enable</Nullable>
    </PropertyGroup>
</Project>
```

**Key Properties**:

- `UseWPF`: Enables WPF framework
- `OutputType: WinExe`: Windows GUI application (no console)
- Project references calculator core library

## XAML Naming Conventions

### Element Naming

**Format**: PascalCase with descriptive suffix indicating control type

**Control Name Suffixes**:

- `Button`: `EvaluateButton`, `ClearButton`
- `TextBox`: `ExpressionTextBox`, `InputTextBox`
- `TextBlock`: `ResultTextBlock`, `TitleTextBlock`
- `ListBox`: `HistoryListBox`
- `Grid`: `MainGrid`, `ButtonGrid`
- `StackPanel`: `TopStackPanel`

**Examples**:

```xaml
✅ <TextBox x:Name="ExpressionTextBox" />
✅ <TextBlock x:Name="ResultTextBlock" />
✅ <Button x:Name="EvaluateButton" Content="=" />
✅ <ListBox x:Name="HistoryListBox" />
```

**Rules**:

- Use `x:Name` for controls that need code-behind access
- Omit `x:Name` for purely layout elements
- Use descriptive, meaningful names
- Suffix indicates control type for clarity

### File Naming

**XAML Files**: PascalCase, matches window/control class name

```
✅ MainWindow.xaml
✅ App.xaml
```

**Code-Behind Files**: Same name as XAML with `.cs` extension

```
✅ MainWindow.xaml.cs
✅ App.xaml.cs
```

**Partial Classes**: Code-behind uses `partial class`

```csharp
namespace Calculator.Wpf
{
    public partial class MainWindow : Window
    {
        // ...
    }
}
```

## XAML Layout Patterns

### Grid-Based Layouts

**Use Grid for complex layouts**:

```xaml
<Grid Margin="10">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="*"/>
        <ColumnDefinition Width="200"/>
    </Grid.ColumnDefinitions>

    <TextBox Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2" />
    <TextBlock Grid.Row="1" Grid.Column="0" />
</Grid>
```

**Grid Sizing**:

- `Auto`: Size to content
- `*`: Proportional sizing (star sizing)
- `200`: Fixed size in pixels

**Grid Span**:

- `Grid.ColumnSpan="2"`: Span multiple columns
- `Grid.RowSpan="2"`: Span multiple rows

### Button Layout Patterns

**Uniform Button Grid**:

```xaml
<Grid Grid.Row="2" Grid.Column="0">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <!-- More rows -->
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="60"/>
        <ColumnDefinition Width="60"/>
        <!-- More columns -->
    </Grid.ColumnDefinitions>

    <Button Grid.Row="0" Grid.Column="0" Content="7" Margin="4" />
    <Button Grid.Row="0" Grid.Column="1" Content="8" Margin="4" />
    <!-- More buttons -->
</Grid>
```

**Benefits**:

- Consistent spacing with `Margin`
- Uniform button sizes with fixed `Width`
- Clear grid-based organization

### ListBox for Dynamic Content

```xaml
<ListBox x:Name="HistoryListBox"
         Grid.Row="3"
         Grid.Column="1"
         MouseDoubleClick="HistoryListBox_DoubleClick"/>
```

**Data Binding**:

```csharp
// Code-behind
HistoryListBox.ItemsSource = history.Select(
    (h, idx) => $"{idx + 1}: {h.Expression} = {h.Result}");
```

## Event Handler Patterns

### Event Handler Naming

**Format**: `ControlName_EventName` or `EventName` (for shared handlers)

**Examples**:

```csharp
✅ private void EvaluateButton_Click(object sender, RoutedEventArgs e) { }
✅ private void Button_Click(object sender, RoutedEventArgs e) { }  // Shared
✅ private void ExpressionTextBox_KeyDown(object? sender, KeyEventArgs e) { }
✅ private void HistoryListBox_DoubleClick(object sender, MouseButtonEventArgs e) { }
```

### Button Click Handlers

**Simple Button Click**:

```xaml
<Button Content="=" Click="EvaluateButton_Click" Margin="4"/>
```

```csharp
private void EvaluateButton_Click(object sender, RoutedEventArgs e)
{
    EvaluateCurrentExpression();
}
```

**Shared Click Handler** (for calculator buttons):

```xaml
<Button Content="7" Click="Button_Click" Margin="4"/>
<Button Content="8" Click="Button_Click" Margin="4"/>
<Button Content="9" Click="Button_Click" Margin="4"/>
```

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is not System.Windows.Controls.Button b) return;
    var content = (b.Content ?? string.Empty).ToString();
    if (string.IsNullOrEmpty(content)) return;

    switch (content)
    {
        case "=":
        case "Eval":
            EvaluateCurrentExpression();
            break;
        case "C":
            ExpressionTextBox.Text = string.Empty;
            ResultTextBlock.Text = string.Empty;
            break;
        case "←":
            // Backspace logic
            break;
        default:
            ExpressionTextBox.Text += content;
            break;
    }
}
```

**Pattern Matching in Event Handlers**:

```csharp
if (sender is not System.Windows.Controls.Button b) return;
```

### Keyboard Event Handlers

**TextBox KeyDown for Enter Key**:

```xaml
<TextBox x:Name="ExpressionTextBox" KeyDown="ExpressionTextBox_KeyDown" />
```

```csharp
private void ExpressionTextBox_KeyDown(object? sender, KeyEventArgs e)
{
    if (e.Key == Key.Enter)
    {
        EvaluateCurrentExpression();
        e.Handled = true;  // Prevent default behavior
    }
}
```

**Benefits**:

- Enter key triggers evaluation (better UX)
- `e.Handled = true` prevents beep sound

### ListBox Item Interaction

**Double-Click to Reuse History**:

```xaml
<ListBox x:Name="HistoryListBox"
         MouseDoubleClick="HistoryListBox_DoubleClick"/>
```

```csharp
private void HistoryListBox_DoubleClick(object sender, MouseButtonEventArgs e)
{
    if (HistoryListBox.SelectedItem is string s)
    {
        // Parse expression from "1: 2+2 = 4" format
        var parts = s.Split(new[] { ": " }, 2, StringSplitOptions.None);
        if (parts.Length == 2)
        {
            var exprAndResult = parts[1].Split(new[] { " = " }, 2, StringSplitOptions.None);
            if (exprAndResult.Length == 2)
            {
                ExpressionTextBox.Text = exprAndResult[0];
            }
        }
    }
}
```

## Code-Behind Patterns

### Constructor Pattern

**Initialize Component and Wire Up Events**:

```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();  // REQUIRED: Initializes XAML
        RefreshHistory();       // Initial data load

        // Programmatic event wiring (alternative to XAML)
        ExpressionTextBox.KeyDown += ExpressionTextBox_KeyDown;
    }
}
```

**Rules**:

- Always call `InitializeComponent()` first
- Initialize data after `InitializeComponent()`
- Wire up events if not defined in XAML

### Separation of Concerns

**UI Logic in Code-Behind**:

```csharp
private void EvaluateCurrentExpression()
{
    var expr = ExpressionTextBox.Text?.Trim();
    if (string.IsNullOrEmpty(expr)) return;

    try
    {
        // Business logic delegated to Evaluator
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

**Business Logic in Core Library**:

```csharp
// In CalculatorLib.Evaluator (separate project)
public static decimal Evaluate(string expression) { /* ... */ }
public static List<(string Expression, decimal Result)> GetHistory() { /* ... */ }
```

**Rules**:

- UI interaction and display: Code-behind
- Calculation and business logic: Core library
- No calculation logic in MainWindow.xaml.cs

### Data Binding to ListBox

**Manual Binding**:

```csharp
private void RefreshHistory()
{
    HistoryListBox.ItemsSource = null;  // Clear previous binding
    var history = Evaluator.GetHistory();
    HistoryListBox.ItemsSource = history.Select(
        (h, idx) => $"{idx + 1}: {h.Expression} = {h.Result}");
}
```

**When to Refresh**:

- After evaluation (success or error)
- On window initialization
- When history might have changed

### Error Handling in WPF

**Display Errors in Result Area**:

```csharp
try
{
    var result = Evaluator.Evaluate(expr);
    ResultTextBlock.Text = result.ToString();
}
catch (Exception ex)
{
    ResultTextBlock.Text = "Error: " + ex.Message;
}
```

**Never Crash the UI**:

- Catch all exceptions in event handlers
- Display user-friendly error messages
- Don't show stack traces or technical details

**Consistent Error Format**:

```
Error: <message>
```

## WPF-Specific Features

### Dependency on Core Library

**Project Reference**:

```xml
<ItemGroup>
    <ProjectReference Include="..\calculator\Calculator.csproj" />
</ItemGroup>
```

**Using Statement**:

```csharp
using CalculatorLib;
```

**Usage**:

```csharp
var result = Evaluator.Evaluate(expression);
var history = Evaluator.GetHistory();
```

### Nullable Reference Types

**Enabled in Project**:

```xml
<Nullable>enable</Nullable>
```

**Handling Nullability**:

```csharp
var expr = ExpressionTextBox.Text?.Trim();  // Text might be null
if (string.IsNullOrEmpty(expr)) return;

var content = (b.Content ?? string.Empty).ToString();  // Content might be null
```

### Culture-Invariant Formatting

**Not Required in WPF**:

- WPF handles culture automatically for display
- Core library uses InvariantCulture internally
- `ToString()` without culture is acceptable in UI

```csharp
✅ ResultTextBlock.Text = result.ToString();  // OK for display
```

**CLI Requires InvariantCulture**:

```csharp
✅ Console.WriteLine(result.ToString(CultureInfo.InvariantCulture));
```

## XAML Best Practices

### Use Descriptive Content

```xaml
✅ <Button Content="Eval" />
✅ <Button Content="C" />  <!-- Clear -->
✅ <Button Content="←" />  <!-- Backspace -->
✅ <Button Content="ANS" />
```

### Consistent Spacing

**Use Margin for Spacing**:

```xaml
<Button Content="7" Margin="4" />  <!-- 4px margin on all sides -->
<TextBox Height="40" Margin="0,6,0,6" />  <!-- 6px top/bottom -->
```

**Grid Margin**:

```xaml
<Grid Margin="10">  <!-- 10px margin around entire grid -->
```

### Sizing Guidelines

**Fixed Sizes for Buttons**:

```xaml
<ColumnDefinition Width="60"/>  <!-- Fixed 60px width -->
```

**Auto-Size for Content**:

```xaml
<RowDefinition Height="Auto"/>  <!-- Size to fit content -->
```

**Fill Remaining Space**:

```xaml
<RowDefinition Height="*"/>  <!-- Take remaining space -->
<ColumnDefinition Width="*"/>
```

## Testing WPF Applications

### Unit Testing WPF

**Challenges**:

- Requires STA thread
- UI automation is complex
- Async dispatcher operations

**Current Testing Approach**:

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

**Focus on Core Logic Testing**:

- Test `Evaluator` thoroughly (unit tests)
- Basic WPF control interaction tests
- Manual testing for complex UI workflows

See `csharp/csharp-testing-instructions.md` for detailed WPF testing patterns.

## Common WPF Patterns

### Input Validation

```csharp
var expr = ExpressionTextBox.Text?.Trim();
if (string.IsNullOrEmpty(expr)) return;  // Silently ignore empty input
```

### Dynamic Content Updates

```csharp
// Update result display
ResultTextBlock.Text = result.ToString();

// Update history list
RefreshHistory();
```

### Resource Cleanup

**Close Windows**:

```csharp
window.Close();  // In tests or when programmatically closing
```

**Dispose Pattern** (if needed):

```csharp
// Not currently needed in calculator WPF app
// But important for apps with IDisposable resources
```

## See Also

- **Project Structure**: project-instructions.md (WPF project configuration)
- **C# Style**: csharp/csharp-style-instructions.md (code-behind naming conventions)
- **Testing**: csharp/csharp-testing-instructions.md (WPF testing patterns)
- **Error Handling**: error-handling-instructions.md (exception handling in UI)
- **Best Practices**: practices-instructions.md (UI performance and quality)

**Note**: This file provides detailed WPF-specific patterns, XAML conventions, and desktop UI development guidelines. For general C# coding standards and project-level organization, refer to the appropriate general instruction files.
