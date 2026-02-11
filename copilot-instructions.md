# Copilot Instructions for Repository

## Tech Stack
- .NET 10+ class libraries, Blazor Server, WPF, console apps, MSTest
- Python 3.11+ (if present)
- Single .sln in src/

## Always Enforce
- Branch from main for /delegate tasks
- Never commit directly to main
- Run `dotnet format`, `dotnet test` before commits
- Use file-scoped namespaces in C#
- MSTest projects: `[TestClass]`, `[TestMethod]`, AAA pattern
- PRs must pass CI (lint + tests)
