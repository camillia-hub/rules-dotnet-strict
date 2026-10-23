# Async

Rules for `async`/`await` in libraries and applications. Async bugs are deadlock bugs; these rules exist to keep them out of review.

## Library code

- Always append `ConfigureAwait(false)` to every `await` in library code.
  Rationale: libraries must not capture the caller's synchronization context; capturing it deadlocks under UI and legacy ASP.NET contexts.

```csharp
// Incorrect
var json = await _client.GetStringAsync(url);

// Correct
var json = await _client.GetStringAsync(url).ConfigureAwait(false);
```

- Always accept a `CancellationToken` as the last parameter of an async library method and pass it through to every downstream async call.
- Never write `async void` outside of UI event handlers; return `Task` or `ValueTask`.
  Rationale: `async void` exceptions cannot be observed by the caller and crash the process.

## Naming

- Always suffix async method names with `Async` (`GetUserAsync`, `SaveChangesAsync`).
  Rationale: the suffix signals "await me" and keeps sync/async overload pairs unambiguous.
- Never give a synchronous method the `Async` suffix.

## Blocking and wrapping

- Never block on async code with `.Result`, `.Wait()`, or `GetAwaiter().GetResult()`; make the call stack async all the way down instead.
  Rationale: blocking on a captured context is the classic ASP.NET/UI deadlock.
- Never wrap CPU-bound work in `Task.Run` inside a library; offloading to a thread pool thread is the caller's decision, not the library's.

```csharp
// Incorrect
public Task<int> ComputeAsync() => Task.Run(() => Compute());

// Correct: expose the synchronous method and let the caller offload.
public int Compute() => /* pure computation */;
```

- Always return `Task.CompletedTask` or `Task.FromResult<T>(value)` from non-async `Task`-returning members instead of `async` stubs with no awaits (avoids CS1998 and a wasted state machine allocation).

## Disposal and streams

- Always use `await using` for `IAsyncDisposable` resources inside async methods.
- Always complete writes and dispose streams with `await`; never dispose a stream while a write may still be in flight.
