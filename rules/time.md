# Time and Dates

Time is a dependency. Treat the clock like any other injected service and treat zones as a presentation concern.

## Clock access

- Never call `DateTime.Now`, `DateTime.UtcNow`, or `DateTimeOffset.Now` directly in production code.
  Rationale: static clocks are a hidden dependency that make tests flaky and behavior non-deterministic.
- Always inject `TimeProvider` through the constructor and call `timeProvider.GetUtcNow()` for the current instant.

```csharp
// Incorrect
public bool IsExpired(ApiToken token) => token.ExpiresAt < DateTime.UtcNow;

// Correct
public bool IsExpired(ApiToken token) => token.ExpiresAt < _timeProvider.GetUtcNow();
```

- Always use `TimeProvider.CreateTimer` and `TimeProvider.GetElapsedTime` for timers and duration measurement in new code.
- Always register `TimeProvider.System` once in the composition root; never reference `TimeProvider.System` from inside business logic.

## Types and zones

- Always store and transmit instants as `DateTimeOffset`, or as a UTC `DateTime` with `Kind == DateTimeKind.Utc`; never persist `DateTimeKind.Unspecified`.
- Never convert to local time anywhere except the outermost presentation boundary.
- Always format user-facing dates and times with an explicit `CultureInfo` and an explicit time zone.
- Never parse date strings without an explicit format and provider; culture-dependent parsing (`DateTime.Parse`) differs across machines and fails silently.

## Testing

- Always use `FakeTimeProvider` (from `Microsoft.Extensions.TimeProvider.Testing`) to advance the clock in tests.
- Never use `Thread.Sleep` or `Task.Delay` in tests to "wait for" time-based behavior; advance the fake clock instead.

```csharp
// Correct
var clock = new FakeTimeProvider();
var sut = new TokenValidator(clock);
clock.Advance(TimeSpan.FromMinutes(30));
Assert.True(sut.IsExpired(token));
```
