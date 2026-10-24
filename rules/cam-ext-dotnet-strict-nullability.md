# Nullability

Hard constraints for nullable reference types (NRT). Every project compiles with `<Nullable>enable</Nullable>`; there is no opt-out for new code.

## Project configuration

- Always enable nullable reference types in every project: `<Nullable>enable</Nullable>` in the csproj or, better, once in `Directory.Build.props`.
  Rationale: annotations only protect code that is compiled with the analysis enabled.
- Always treat nullable warnings as errors in CI (`<WarningsAsErrors>nullable</WarningsAsErrors>`).
  Rationale: an ignorable nullability warning is a `NullReferenceException` scheduled for production.
- Never add `#nullable disable` or `<Nullable>disable</Nullable>` to new code; migrate legacy files instead of copying the opt-out forward.

## Declaring intent

- Always declare reference-type parameters, return values, and properties as non-nullable unless `null` is a documented, expected value.
- Always use `T?` when absence is legitimate; never return a "magic" null from a non-nullable signature.
- Always validate arguments at public boundaries with `ArgumentNullException.ThrowIfNull(arg)`.

```csharp
// Incorrect
public string GetDisplayName(User user) => user.Name;

// Correct
public string GetDisplayName(User user)
{
    ArgumentNullException.ThrowIfNull(user);
    return user.Name;
}
```

## Suppression and flow analysis

- Never use the null-forgiving operator `!` to silence the compiler in production code.
  Rationale: `!` asserts safety with zero evidence; restructure the code so the compiler can prove it instead.
- Never suppress `CS86xx` diagnostics with `#pragma warning disable`; fix the flow analysis that produced the warning.
- Always prefer pattern matching (`is null`, `is not null`, `is { } x`) over a manual check followed by `!`.

```csharp
// Incorrect
var name = user!.Name;

// Correct
if (user is null) return NotFound();
var name = user.Name;
```

- Always initialize non-nullable properties at construction; use `required` members when the compiler cannot see the initialization path.
