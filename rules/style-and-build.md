# Style and Build

Style here is not taste; it is the set of mechanical constraints that keep diffs small and builds honest.

## File layout

- Always use file-scoped namespaces (`namespace Acme.Billing;`).
  Rationale: one namespace per file is the norm; the file-scoped form removes a level of indentation from every line.

```csharp
// Incorrect
namespace Acme.Billing
{
    public class Invoice { }
}

// Correct
namespace Acme.Billing;

public class Invoice { }
```

- Never use `#region` / `#endregion`.
  Rationale: regions hide size and cohesion problems; a class that "needs" regions needs to be split.
- Always keep one public type per file, with the file named after the type.
- Always let `.editorconfig` sort usings (`System.*` first) and never commit unused usings.
- Always use `var` when the type is apparent from the right-hand side and spell the type when it is not; encode the choice in `.editorconfig` so reviews never debate it.

## Build hygiene

- Always build with `<TreatWarningsAsErrors>true</TreatWarningsAsErrors>` in CI configurations.
- Always enable the .NET analyzers (`<EnableNETAnalyzers>true</EnableNETAnalyzers>`, `<AnalysisLevel>latest</AnalysisLevel>`) and resolve every diagnostic.
- Always restore with locked mode in CI (`dotnet restore --locked-mode`) so dependency resolution is reproducible from the committed lock file.
- Never merge code with analyzer or compiler warnings; a warning-free build is a merge gate, not a stretch goal.
- Never add `#pragma warning disable` without an inline justification comment and a linked issue; restore the warning immediately after the smallest possible span.

## Formatting and hygiene

- Always verify formatting in CI with `dotnet format --verify-no-changes`; never hand-format against the `.editorconfig`.
- Always end files with a single trailing newline and no trailing whitespace (`.editorconfig` enforced).
- Never commit commented-out code; delete it - version control is the history.
- Never commit a `TODO` comment without an owner and a linked issue.
