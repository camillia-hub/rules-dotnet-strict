# Types and API Design

## DTOs and messages

- Always model DTOs, API contracts, and event messages as `record` types (or `record struct` for tiny values).
  Rationale: records provide value equality, immutability, and deconstruction for free - exactly what a data carrier needs.
- Always use positional parameters or `init`-only properties on records; never add mutable `set` accessors to a DTO.

```csharp
// Incorrect
public class UserDto
{
    public string Name { get; set; } = "";
}

// Correct
public sealed record UserDto(string Name);
```

- Never put persistence, I/O, or transport-specific formatting behavior on a DTO; data carriers carry data.
- Always map ORM/persistence entities to DTOs at the boundary; never leak EF Core entities through an API response.
- Always expose collections on DTOs as `IReadOnlyList<T>` (or an immutable array) initialized once; never expose a mutable `List<T>` with a setter.
- Always make DTO property nullability match the contract exactly; never mark everything nullable just to dodge mapping errors.

## Encapsulation

- Never declare public or protected fields; always use properties.
  Rationale: fields cannot be intercepted, validated, or versioned; changing a field into a property later is a binary-breaking change.
- Always seal classes that are not explicitly designed for inheritance (`sealed class`).
  Rationale: sealing prevents fragile-base-class bugs and enables runtime devirtualization.
- Always choose the most restrictive accessibility that works: `private` before `internal` before `protected` before `public`.
- Never return `null` for a collection or string member; return an empty collection or `string.Empty`.
  Rationale: callers should not need a null check for a value that has a natural empty state.

## Construction

- Always establish invariants in the constructor or a static factory; never allow an instance to exist in an invalid state.
- Always use primary constructors for simple dependency capture; keep non-trivial validation in an explicit constructor or factory.
- Never perform I/O or call virtual members from a constructor; virtual dispatch during construction can run before the derived type is initialized.
- Always keep constructors fast; expensive work belongs in an explicit initialization step the caller can see, time, and await.
