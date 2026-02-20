---
applyTo: "src/**/*.csproj"
description: "Tells Copilot to follow the csharp-project-scaffolding skill when creating or modifying .csproj files under src/"
---

## Context

Any `.csproj` file under `src/` must be scaffolded according to the conventions in [.github/skills/csharp-project-scaffolding/SKILL.md](../skills/csharp-project-scaffolding/SKILL.md). That skill is the single source of truth for project structure, NuGet packages, and solution integration. Do not invent structure — read and follow the skill.

> For full conventions and rationale, see [csharp-project-scaffolding/SKILL.md](../skills/csharp-project-scaffolding/SKILL.md).

## Do
- Read `csharp-project-scaffolding/SKILL.md` before generating anything.
- Ask the four pre-flight questions from the skill before creating any files.
- Run `dotnet build --configuration Release` after scaffolding; fix all errors and warnings.

## Avoid
- Avoid creating placeholder files (`Class1.cs`, stub `Program.cs`) — the skill defines exactly what files to generate.
- Avoid skipping build verification — do not present the project as done until the build is clean.