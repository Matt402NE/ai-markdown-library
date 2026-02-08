# GitHub Copilot

GitHub Copilot leverages `.github/` files to provide repo-specific context across Visual Studio Enterprise, VS Code, CLI, and GitHub.com. This setup enforces conventions for your C#/Python solutions while enabling `/delegate` workflows.

### Structure Summary

| Component         | Location                                 | Structure                            |
| ----------------- | ---------------------------------------- | ------------------------------------ |
| **Repo-wide**     | `.github/copilot-instructions.md`        | Single root file                     |
| **Path-specific** | `.github/instructions/*.instructions.md` | **Flat files** (name describes path) |
| **Agents**        | `.github/agents/*.agent.md`              | **Flat files**                       |
| **Skills**        | `.github/skills/name/SKILL.md`           | **Folders**                          |

## Step 1: Create Base Folder Structure

Initialize each repo with this layout (add/commit/push to `main`):

```
.github/
├── copilot-instructions.md           # Repo-wide rules (always active)
├── instructions/                     # Path-specific guidance
│   └── src/                          # For src/ code
├── agents/                           # Custom agents
│   └── ci-debugger.agent.md          # Generic agent example
├── skills/                           # Reusable task skills
│   └── commit-message.SKILL.md       # Draft commits
├── workflows/                        # GitHub Actions CI/CD
│   ├── ci.yml                        # Build/test PRs
│   └── delegate.yml                  # /delegate validation
├── ISSUE_TEMPLATE/                   # GitHub issues
│   └── feature-request.md
├── PULL_REQUEST_TEMPLATE.md
└── dependabot.yml                    # Auto-updates
docs/                                 # Architecture, runbooks
src/                                  # Your .sln + .csproj projects
README.md
```


There are three concepts—**Custom Agents, Agent Skills, and Repository Custom Instructions**—that impact how Copilot works in Visual Studio or with the Copilot CLI.


### **Repository Custom Instructions (`.github/copilot-instructions.md`)**

- **Purpose:** Always-on, repo-specific guidance.
- **Use case:** Enforce **stable, repo-wide norms**: coding style, architecture notes, build/test/deploy commands, libraries to prefer/avoid.
- **Behavior:** These instructions are automatically applied whenever Copilot interacts with the repo. They’re passive but persistent.

**Rule of thumb:**

> Use custom instructions for anything that should **always apply**, regardless of which agent or skill is being used.

**Example:**

- “All commits must pass `lint` and `unit-tests` before merging.”
- “Use `TypeScript` for backend services and `React` for frontend components.”


### **Agent Skills (`.github/skills` or `.claude/skills`)**

- **Purpose:** Encapsulate **specific, reusable capabilities** that agents can call on-demand.    
- **Use case:** Task-oriented, modular logic that can be shared across agents and repositories.
- **Behavior:** Skills are **invoked automatically** by the agent if relevant or can be explicitly invoked in Copilot CLI. They are portable.

**Rule of thumb:**

> Use skills for **task-specific logic** that can be reused in multiple contexts.

**Example:**

- `github-actions-failure-debugging` skill that knows how to parse logs and suggest fixes.    
- `webapp-testing` skill that runs integration tests and reports issues.    


### **Custom Agents**

- **Purpose:** Define a **persona or workflow orchestrator** that can combine skills and instructions.
- **Use case:** When you want **a named, persistent “agent”** that handles complex workflows end-to-end.
- **Behavior:** Custom agents select skills, follow repo instructions, and manage task orchestration in a consistent way.

**Rule of thumb:**

> Use custom agents when you want **a consistent helper** that can handle multi-step workflows or complex tasks.

**Example:**

- A `CI-Debugger` agent that automatically:    
    1. Reads failing GitHub Actions logs.
    2. Applies `github-actions-failure-debugging` skill.
    3. Suggests code fixes or workflow updates.


### **How They Work Together**

For a workflow like **“diagnose and fix failing GitHub Actions workflows in a monorepo”:**

Component | Role -- | -- Repository Custom Instructions | Provide project-specific context (CI setup, test commands, repo structure). Agent Skills | Encapsulate the task-specific logic (github-actions-failure-debugging, parsing logs, suggesting fixes). Custom Agent | Orchestrates the workflow, deciding when and which skills to apply, interacts with CLI prompts, and provides a “persona” that understands the repo.

**Recommended pattern:**

1. **Always define repo-wide instructions** for consistent context.
2. **Create modular skills** for repeatable, task-specific logic.
3. **Use custom agents** only when you need a named, orchestrated persona to handle complex workflows or multiple skills together.


## Step 2: Add copilot-instructions.md

Create `.github/copilot-instructions.md` with these core rules:

```
# Repo Instructions for Copilot

## Tech Stack
- .NET 8+ class libraries, Blazor Server, WPF, console apps, MSTest
- Python 3.11+ (if present)
- Single .sln in src/

## Always Enforce
- Branch from main for /delegate tasks
- Never commit directly to main
- Run `dotnet format`, `dotnet test` before commits
- Use file-scoped namespaces in C#
- MSTest projects: `[TestClass]`, `[TestMethod]`, AAA pattern
- PRs must pass CI (lint + tests)

## Commands Copilot Knows
- Build: `dotnet build src/*.sln`
- Test: `dotnet test src/*.sln --logger trx`
- Format: `dotnet format src/`
```

This auto-applies everywhere Copilot runs.


## Step 3: Add Specific Instructions


**Path-specific instructions** use a **folder-per-context structure** with `*.instructions.md` files directly under `.github/instructions/` (no subfolders needed for basic use)

```
.github/instructions/
├── src.instructions.md              # Applies to src/ files
├── tests.instructions.md            # Applies to test files
├── workflows.instructions.md        # Applies to .github/workflows/
└── *.instructions.md                # Any descriptive name
```

### Path Matching Globs​
Each `*.instructions.md` starts with `path: <glob-pattern>`:

```
path: src/**                        # All files under src/
path: **/*.cs                       # All C# files anywhere
path: .github/workflows/*.yml       # Only workflow files
path: tests/**/MSTest*.cs           # MSTest files only
```

### How Copilot Uses Instructions​

1. **Always**: `.github/copilot-instructions.md`
2. **When editing** `src/MyLib/Calculator.cs`:
    - Loads `src.instructions.md` (path match)
    - Combines with repo-wide instructions

## Step 3: Create Generic CI-Debugger Agent

Create `.github/agents/ci-debugger.agent.md`:

```
---
name: CI Debugger
description: Analyzes failing GitHub Actions, suggests fixes
tools: [read, search, edit]
target: [vscode, github-copilot]
---

You debug CI failures in .NET/Python repos.

1. Read failing workflow logs from `.github/workflows/`
2. Check `src/` build/test errors  
3. Suggest fixes to code or YAML
4. Create PR from `main` via /delegate

Always reference `copilot-instructions.md`.

```

GitHub Copilot **does not support a sub-folder-per-agent structure** as it does for skills. Agents follow a **flat file naming convention** and all exist under the `agents/` folder.


- [Frontmatter Options](https://code.visualstudio.com/docs/copilot/customization/custom-agents#custom-agent-file-structure)




## Step 4: Add Commit Message Skill

Create `.github/skills/commit-message.SKILL.md`:

```
# Commit Message Skill

Generate conventional commits:

<type>[optional scope]: <description>

[optional body]

[optional footer]

Types: feat, fix, docs, style, refactor, test, chore

Examples:
- `feat: add user authentication`
- `fix(tests): resolve MSTest filter failures`
```

GitHub Copilot supports both Claude-style **skills in individual folders** (with `SKILL.md`) and flat `.SKILL.md` files, but the **folder-per-skill structure is the recommended modern approach** across VS Code, CLI, and GitHub.com.

```
.github/skills/
├── commit-message/                   # Each skill = folder
│   ├── SKILL.md                      # Required instruction file
│   └── examples/                     # Optional helpers
│       └── conventional-commits.md
└── mstest-generator/                 # Future skill
    └── SKILL.md
```


## Step 5: Daily Workflows

## Visual Studio Enterprise

1. Open `.sln` → Copilot Chat (`Ctrl+``)    
2. `@ci-debugger Explain this Actions failure` ([how to](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents))
3. Chat auto-uses `.github/copilot-instructions.md`



## Online Resources

- [dfinke/awesome-copilot-chatmodes](https://github.com/dfinke/awesome-copilot-chatmodes)
- [sachin-aag/Writing-system-prompt](https://github.com/sachin-aag/Writing-system-prompt)
- 