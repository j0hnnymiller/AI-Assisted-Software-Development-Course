# Issue 010: Add Input Validation in MainWindow Event Handlers

## Description

The WPF `MainWindow.xaml.cs` event handlers have several minor issues with input validation and null checking that could lead to unexpected behavior.

## Issues Found

### 1. Nullable Sender Parameter Not Validated

In `MainWindow.xaml.cs`, line 18:

```csharp
private void ExpressionTextBox_KeyDown(object? sender, KeyEventArgs e)
{
    if (e.Key == Key.Enter)
    {
        EvaluateCurrentExpression();
        e.Handled = true;
    }
}
```

The `sender` parameter is marked nullable but never used. This is fine, but for consistency and potential future use, consider:

- Removing the `?` if not needed
- Adding validation if needed

**Impact**: Low - `sender` is not currently used

### 2. Potential Null Reference in Button_Click

In `MainWindow.xaml.cs`, line 54:

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is not System.Windows.Controls.Button b) return;
    var content = (b.Content ?? string.Empty).ToString();
```

The code safely handles null `b.Content`, but `ToString()` on a null result would still throw. The `?? string.Empty` handles this, but it's not immediately obvious.

**Better approach**:

```csharp
var content = (b.Content?.ToString()) ?? string.Empty;
```

This makes it clear that we're calling `ToString()` only if `Content` is not null.

**Impact**: Low - Current code works but could be clearer

### 3. No Validation for Long Expressions

There's no limit on the length of the expression in `ExpressionTextBox`. Very long expressions could:

- Cause UI slowdown
- Lead to stack overflow in nested function parsing
- Create very large history entries

**Impact**: Medium - Could affect performance and user experience

### 4. No Validation for Result Display Overflow

In `MainWindow.xaml.cs`, line 40:

```csharp
ResultTextBlock.Text = result.ToString();
```

Very large numbers could overflow the display area. Consider:

- Truncating with ellipsis
- Using scientific notation
- Adding a tooltip with full value

**Impact**: Low - Cosmetic issue

## Proposed Solutions

### Fix 1: Clean Up Nullable Parameter

```csharp
private void ExpressionTextBox_KeyDown(object sender, KeyEventArgs e)
{
    if (e.Key == Key.Enter)
    {
        EvaluateCurrentExpression();
        e.Handled = true;
    }
}
```

Or if we want to validate:

```csharp
private void ExpressionTextBox_KeyDown(object? sender, KeyEventArgs e)
{
    if (sender == null) return;  // or throw
    if (e.Key == Key.Enter)
    {
        EvaluateCurrentExpression();
        e.Handled = true;
    }
}
```

### Fix 2: Clearer Null Handling

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    if (sender is not System.Windows.Controls.Button b) return;
    var content = b.Content?.ToString() ?? string.Empty;
    if (string.IsNullOrEmpty(content)) return;

    // ... rest of logic
}
```

### Fix 3: Add Expression Length Limit

```csharp
private void EvaluateCurrentExpression()
{
    var expr = ExpressionTextBox.Text?.Trim();
    if (string.IsNullOrEmpty(expr)) return;

    const int MaxExpressionLength = 1000;
    if (expr.Length > MaxExpressionLength)
    {
        ResultTextBlock.Text = $"Error: Expression too long (max {MaxExpressionLength} characters)";
        return;
    }

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

### Fix 4: Better Result Display

```csharp
var result = Evaluator.Evaluate(expr);
var resultStr = result.ToString();

// If result is very long, use scientific notation
if (resultStr.Length > 20)
{
    resultStr = result.ToString("E6");  // Scientific notation with 6 decimals
}

ResultTextBlock.Text = resultStr;
ResultTextBlock.ToolTip = result.ToString();  // Full value in tooltip
```

## Files Affected

- `calculator-wpf/MainWindow.xaml.cs`

## Test Coverage

Add UI tests (if test framework supports WPF testing):

```csharp
[TestMethod]
public void MainWindow_HandlesVeryLongExpression()
{
    var window = new MainWindow();
    window.ExpressionTextBox.Text = new string('1', 2000) + "+1";
    // Should not hang or crash
    window.EvaluateCurrentExpression();
    Assert.IsTrue(window.ResultTextBlock.Text.StartsWith("Error"));
}

[TestMethod]
public void MainWindow_HandlesVeryLargeResult()
{
    var window = new MainWindow();
    window.ExpressionTextBox.Text = "99999999999999999999999999";
    window.EvaluateCurrentExpression();
    // Should display without crashing
    Assert.IsFalse(string.IsNullOrEmpty(window.ResultTextBlock.Text));
}
```

## Priority

Low - These are minor code quality issues that don't affect core functionality

## Related Issues

- Issue #004: WPF code-behind refactor or document
- Issue #009: Potential infinite loop in ReplaceFunctions (expression length limit could help)
