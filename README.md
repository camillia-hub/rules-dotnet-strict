# .NET Strict Rules

A Camillia rules pack that enforces strict, modern C#/.NET practice as hard constraints: nullable-safe, async-correct, analyzer-clean code.

## Rules at a glance

- **Nullability** (`rules/cam-ext-dotnet-strict-nullability.md`) - nullable reference types always on, nullable warnings as errors, no null-forgiving `!` abuse, no `#nullable disable` in new code.
- **Async** (`rules/cam-ext-dotnet-strict-async.md`) - `ConfigureAwait(false)` in libraries, `Async` suffix naming, no `async void`, never block with `.Result` / `.Wait()`, cancellation tokens everywhere.
- **Time and dates** (`rules/cam-ext-dotnet-strict-time.md`) - no `DateTime.Now`; inject `TimeProvider`, store instants as `DateTimeOffset` / UTC, fake the clock in tests.
- **Types and API design** (`rules/cam-ext-dotnet-strict-types.md`) - records for DTOs, immutable data carriers, no public fields, sealed classes, restrictive accessibility.
- **Style and build** (`rules/cam-ext-dotnet-strict-style-and-build.md`) - file-scoped namespaces, no `#region`, analyzer-clean builds with warnings as errors, formatting verified in CI.

## Install

Install from the Camillia Community Hub. On install, the rule files are placed under `.workspace/rules/` in your workspace and apply to every agent session there.

## License

MIT - see [LICENSE](LICENSE). Copyright 2026 Camillia contributors.
