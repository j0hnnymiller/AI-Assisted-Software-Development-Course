Title: Document WPF code-behind usage or plan MVVM refactor

Problem

`calculator-wpf/MainWindow.xaml.cs` contains non-trivial UI logic (event handlers, EvaluateCurrentExpression, RefreshHistory). However `.vscode/instructions/wpf.instructions.md` currently recommends preferring MVVM and minimal code-behind without acknowledging the current code state.

Why it matters

- Contributors may be surprised by the code-behind and attempt to move logic incorrectly.
- A lack of clear guidance slows contributors or causes inconsistent UI changes.

Suggested fix

- Update `wpf.instructions.md` to explicitly state the repository currently uses code-behind for UI logic and recommend an incremental refactor path to MVVM (introduce ViewModel, move logic incrementally, add binding tests).
- Optionally: open a refactor issue to migrate MainWindow logic into a `MainWindowViewModel` with tests.

Acceptance criteria

- Instruction acknowledges current code-behind and includes a short refactor plan.
- If refactor is desired, create follow-up issue(s) to implement it incrementally.
