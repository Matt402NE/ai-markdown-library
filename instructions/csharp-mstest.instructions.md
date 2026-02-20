---
applyTo: src/**/*.cs,tests/**/*.cs
---
# C# MSTest Instructions

When writing, scaffolding, or reviewing C# test code in this project, follow the C# MSTest Skill defined in:

```
.github/skills/csharp-mstest/SKILL.md
```

Read and apply all rules from that skill before generating any test code.

## Quick Reference

- **Framework**: MSTest with Moq (`MockBehavior.Strict` by default)
- **Naming**: `{UnitOfWork}_{StateUnderTest}_{ExpectedBehavior}` (Roy Osherove convention)
- **Structure**: One test class per file, `#region` directives, XML documentation on every class and method
- **Assertions**: Always include a plain string message, use specialized asserts (StringAssert, CollectionAssert, Assert.HasCount)
- **Exceptions**: Try/catch with bool flag — do not use `Assert.ThrowsException<T>`
- **Records**: Object initializer syntax — do not use `with` expressions
- **Async**: Always return `Task`, never `void`
- **Nullable**: Test fields initialized in `[TestInitialize]` must be declared nullable — no `null!`

## Project Scoping

| Project Pattern                   | Purpose                                                            | Runs On            |
| --------------------------------- | ------------------------------------------------------------------ | ------------------ |
| `{SolutionName}.Test.Unit`        | Unit tests only. No external dependencies or database connections. | Every branch push  |
| `{SolutionName}.Test.Integration` | Integration tests. May use external dependencies.                  | Pull requests only |

## What NOT to Do

Do not use any of the following in generated test code:

- `Assert.ThrowsException<T>` or `Assert.ThrowsExceptionAsync<T>`
- `with` expressions on records
- `Assert.IsTrue(x.Contains(...))` when a specialized assert exists
- `Assert.AreEqual` for collection counts (use `Assert.HasCount`)
- `async void` test methods
- `null!` for test class field initialization
- String interpolation or lambdas in Assert messages
- Full exception `Message` string assertions
- Class name in the test method name
- Multiple test classes in a single file