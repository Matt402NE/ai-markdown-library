# AI Markdown Library

This library contains markdown files that are used with AI Assistants.  The contents of this repository should be copied to global folders (`.copilot`) or local repositories (e.g. `.github` or `.claude`). 

## GitHub Copilot

GitHub Copilot leverages `.github/` files to provide repo-specific context across Visual Studio Enterprise, VS Code, CLI, and GitHub.com. This setup enforces conventions for your C#/Python solutions while enabling `/delegate` workflows.

The [GitHub Copilot Overview](copilot-github-overview.md) explains the folder structure and how it markdown (.md) files work together. 

#### Global GitHub Agent Skills

[Copilot supports](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills):

- Project skills, stored in your repository (`.github/skills` or `.claude/skills`)
- Personal skills, stored in your home directory and shared across projects (`~/.copilot/skills` or `~/.claude/skills`).  Personal skills are supported by Copilot coding agent and GitHub Copilot CLI only

The **`~/.copilot`** can be found under `C:\Users\YourName\.copilot` on Windows or `/home/username/.copilot` on Linux/macOS.

### Structure Summary

| Component         | Location                                        | Structure                            |
| ----------------- | ----------------------------------------------- | ------------------------------------ |
| **Repo-wide**     | `.github/copilot-instructions.md`               | Single root file                     |
| **Path-specific** | `.github/instructions/{{name}}.instructions.md` | **Flat files** (name describes path) |
| **Agents**        | `.github/agents/{{name}}.agent.md`              | **Flat files**                       |
| **Skills**        | `.github/skills/{{name}}/SKILL.md`              | **Folders**                          |

### Repository Custom Instructions (`.github/copilot-instructions.md`)

- **Purpose:** Always-on, repo-specific guidance.
- **Use case:** Enforce **stable, repo-wide norms**: coding style, architecture notes, build/test/deploy commands, libraries to prefer/avoid.
- **Behavior:** These instructions are automatically applied whenever Copilot interacts with the repo. They’re passive but persistent.
#### Rule of thumb:

> Use custom instructions for anything that should **always apply**, regardless of which agent or skill is being used.

**Example:**

- “All commits must pass `lint` and `unit-tests` before merging.”
- “Use `TypeScript` for backend services and `React` for frontend components.”


## How They Work Together

For a workflow like **“diagnose and fix failing GitHub Actions workflows in a monorepo”:**

| **Component**                      | **Role**                                                                                                                                                |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Repository Custom Instructions | Provide project-specific context (CI setup, test commands, repo structure).                                                                         |
| Agent Skills                   | Encapsulate the task-specific logic (github-actions-failure-debugging, parsing logs, suggesting fixes).                                             |
| Custom Agent                   | Orchestrates the workflow, deciding when and which skills to apply, interacts with CLI prompts, and provides a “persona” that understands the repo. |

**Recommended pattern:**

1. **Always define repo-wide instructions** for consistent context.
2. **Create modular skills** for repeatable, task-specific logic.
3. **Use custom agents** only when you need a named, orchestrated persona to handle complex workflows or multiple skills together.

## Choosing Between Instruction and Prompt Files

- Instruction Files: Best for repository-wide guidance and long-term standards across multiple contributors and projects.
- Prompt Files: Best for local, session-based guidance or for specific functionality in a single file or segment.
- Combine Both: For maximum results, use instruction files for your overall project and supplement with prompt files in areas that need extra clarity or specificity.


## Online Resources

* [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD)
* [automazeio/ccpm](https://github.com/automazeio/ccpm)