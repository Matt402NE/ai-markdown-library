---
applyTo: _.Test.Unit/**/_.cs, _.Test.Integration/**/_.cs
description: MSTest unit and integration test conventions for C# projects.
---
---


## Context

These files belong to MSTest test projects (`{ProjectSlug}.Test.Unit` or `{ProjectSlug}.Test.Integration`). Unit tests must be fast, self-contained, and must never connect to external databases or services — those concerns belong in the integration project. Every test class targets exactly one class under test.


> For full conventions and rationale, see [csharp-mstest/SKILL.md](../skills/csharp-mstest/SKILL.md).

## Do

### Naming

- Name every test method with exactly three underscore-separated PascalCase sections: `{UnitOfWork}_{StateUnderTest}_{ExpectedBehavior}` (Roy Osherove convention).
- Name test files `{ClassName}Tests.cs` — one test class per file, one file per class under test.
- Mirror the class-under-test namespace, prefixed with `{SolutionName}.Test.Unit` or `{SolutionName}.Test.Integration` (e.g. `Solution.Services.Billing` → `Solution.Test.Unit.Services.Billing`).

### Structure

- Include `[TestClass]` on the class and `[TestMethod]` on every test method.
- Organize methods with `#region Public Methods` and `#region Private Methods` directives.
- Place `[TestInitialize]` and `[TestCleanup]` methods before the `#region Public Methods` block when shared setup is needed.
- Write every test method body with all three AAA comment labels present, even when a section is empty:
    
    ```
    // Arrange (Given)// Act (When)// Assert (Then)
    ```
    

### XML Documentation

- Add an XML `<summary>` to every test class using `<see cref="ClassName"/>`.
- Add an XML `<summary>` to every test method that describes the behavior in plain English — do not restate the method name.

### Assertions

- Include a plain string literal message as the last argument on every `Assert`, `StringAssert`, and `CollectionAssert` call.
- Format every multi-argument Assert call with each argument on its own line.
- Use `StringAssert.Contains()` / `StringAssert.StartsWith()` / `StringAssert.EndsWith()` / `StringAssert.Matches()` for string condition checks.
- Use `CollectionAssert.Contains()` / `CollectionAssert.IsSubsetOf()` for collection membership checks.
- Use `Assert.HasCount()` to assert a specific collection count.
- Use `Assert.IsTrue(collection.Any(x => condition), message)` for LINQ Any/All conditions — no specialized alternative exists.

### Exception Testing

- Test expected exceptions with a try/catch block that sets a `bool exceptionThrown = false` flag or captures the typed exception, then assert after the catch.
- When asserting on caught exceptions, verify only specific properties (`ParamName`, `HResult`) — never assert against the full `Message` string.

### Async Tests

- Declare all async test methods as `async Task` — never `async void`.
- Pass `CancellationToken.None` when the method under test accepts a `CancellationToken`.

### Records

- Create new record instances in tests using object initializer syntax — copy each property explicitly so the test is self-contained and readable.

### Nullable Reference Types

- Declare test class fields that are initialized in `[TestInitialize]` as nullable (e.g. `private MyClass? _instance;`).

### Mocking

- Use Moq for mocking dependencies. Default to `MockBehavior.Strict` so unexpected calls throw immediately.
- Use `MockBehavior.Loose` only for dependencies such as `ILogger<T>` where many methods are called and most are not relevant to the test.

## Avoid

- **Never use `Assert.ThrowsException<T>` or `Assert.ThrowsExceptionAsync<T>`** — use try/catch blocks to allow richer, property-level assertions on the caught exception.
- **Never use `with` expressions on records** — use object initializer syntax (e.g. `new MyRecord { A = x, B = y }`) so all property values are explicit at the point of construction.
- **Never use `Assert.IsTrue(x.Contains(...))` for strings or collections** — use `StringAssert.Contains` or `CollectionAssert.Contains` to avoid MSTEST0037 warnings and get clearer failure messages.
- **Never use `Assert.AreEqual` to compare a collection count** — use `Assert.HasCount` instead (avoids MSTEST0037).
- **Never omit the message parameter** on any Assert call — messages are required for all assertions.
- **Never put more than one test class in a single file.**
- **Never include the class name in the test method name** — the test class already identifies the class under test.
- **Never write an XML summary that restates the method name segments** — describe the behavior and why it matters.
- **Never use `null!` to initialize test class fields** — declare them as nullable and initialize in `[TestInitialize]`.
- **Never use inline lambdas or string interpolation in Assert messages** — use plain string literals only (no `$"..."`, no `() => ...`).
- **Never connect to real external databases or services in `*.Test.Unit`** — use in-memory alternatives or mocks.

## Examples

### Exception test — try/catch pattern

```csharp
[TestMethod]
public void Process_NullRequest_ThrowsArgumentNullException()
{
    // Arrange (Given)
    ArgumentNullException? caughtException = null;

    // Act (When)
    try
    {
        _instance!.Process(null!);
    }
    catch (ArgumentNullException ex)
    {
        caughtException = ex;
    }

    // Assert (Then)
    Assert.IsNotNull(
        caughtException,
        "Process should throw ArgumentNullException when request is null.");
    Assert.AreEqual(
        "request",
        caughtException.ParamName,
        "Exception should identify the 'request' parameter.");
}
```