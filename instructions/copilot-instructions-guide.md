# GitHub Copilot Instructions Guide

> A developer guide to customizing Copilot behavior in your repository.
> 
> **Intended audience:** Developers and maintainers responsible for defining repository conventions and customizing Copilot behavior. If you are a contributor rather than a maintainer, this guide explains the files that shape what Copilot suggests in this repo — you should follow them, not necessarily modify them.

---

## Overview

GitHub Copilot can be customized with project-specific instructions that shape how it generates code, answers questions, and assists across your repository. There are two primary mechanisms: **repository-wide instructions** and **path-specific instructions**. These instruction files are honored by Copilot in supported editors as well as by the Copilot CLI when run in this repository, so commands like `gh copilot` benefit from the same conventions. The CLI primarily respects repository-level instruction context during generation tasks — feature parity with editor integrations (such as prompt files or agent workflows) may vary. Note that the CLI loads repository instructions only when executed inside that repository's directory — running it from a parent folder or an unrelated directory will not pick up these files.

---

## For Contributors

If you are contributing to this repository rather than maintaining it, you don't need to edit instruction files as part of normal work.

- **Read them once** to understand the conventions Copilot will try to follow when you use it in this repo.
- **When Copilot suggests something that conflicts with these rules**, treat the instruction files as the source of truth and adjust the generated code accordingly.
- **If you notice an instruction is outdated or incorrect**, raise it with a maintainer rather than working around it — stale instructions affect everyone on the team.

---

## Important: Instructions Are Advisory

Copilot instructions influence code generation but do not enforce rules. They are contextual hints, not a validation or rule engine — there is no guarantee that generated code will comply with every instruction, and violations will not produce errors or warnings.

In practice this means:

- Instructions make the desired output *more likely*, not certain.
- Instruction files influence future code generation only — they do not retroactively modify existing code or automatically refactor your repository.
- Instruction files have the strongest effect on Copilot Chat, edit operations, and agent-based workflows. Inline code completions may not always fully reflect instruction content, especially for short suggestions.
- Generated code should still be reviewed against your conventions, exactly as you would review any other contribution.
- If Copilot consistently ignores a rule, the instruction is probably too vague, contradicted by another file, or buried in noise — not a tool malfunction.

---

## What Instruction Files Cannot Do

Understanding the boundaries is as important as knowing the capabilities:

- **Cannot enforce architectural rules** — an instruction saying "never reference Infrastructure from Domain" will not prevent Copilot from generating a violating import if the surrounding context makes it seem reasonable.
- **Cannot validate or block code** — there is no mechanism for instructions to reject a suggestion or flag a violation.
- **Cannot replace linters or analyzers** — Roslyn analyzers, EditorConfig rules, and CI checks are the right tools for enforcing formatting and style constraints. Instructions are not.
- **Cannot replace code review** — generated code should be reviewed with the same scrutiny as any other contribution, regardless of how detailed the instructions are.
- **Cannot guarantee consistency** — two identical prompts may produce different output even with the same instruction files loaded.
- **Cannot fully compensate for weak stack coverage** — in ecosystems where Copilot has less training data or weaker support, even precise instructions may have limited effect. Treat them as guidance, not a guarantee that Copilot deeply understands your stack.

Instructions work best as a way to steer generation toward correct patterns, not as a substitute for tooling that enforces them.

---

## When You Probably Don't Need Instruction Files

Instruction files add the most value in medium-to-large repositories with established conventions that aren't fully captured by existing tooling.

You likely don't need them if:

- The repository is small and self-consistent — the existing code already demonstrates the conventions clearly.
- All critical rules are already enforced by analyzers, linters, and CI checks with no gaps worth bridging.
- The team is a single developer with no need to share conventions via committed files.

In small or early-stage projects, instruction files may add complexity without meaningfully improving output quality. Start without them and introduce them when you notice Copilot consistently generating code that diverges from your conventions in ways that tooling doesn't catch.

---

## Repository-Wide Instructions

The file `.github/copilot-instructions.md` is always loaded by Copilot for this repository, regardless of which file in the repo you are editing. Create it as a plain Markdown file directly in the repository and commit it — Copilot picks it up automatically with no extra registration required. Use it for conventions that apply across the entire codebase. These instructions are not limited to your local editor — GitHub-hosted Copilot features such as code review on pull requests may also respect this file, depending on your Copilot plan and feature availability.

**Good candidates for repo-wide instructions:**

- Target framework and language version (e.g., .NET 9, C# 13)
- Preferred patterns (e.g., `Result<T>` over exceptions, no nullable reference suppression)
- Naming conventions and code style rules
- Architecture rules (e.g., "never reference Infrastructure from Domain")
- Testing framework in use (e.g., MSTest, xUnit, NSubstitute)
- Logging library and usage conventions
- Security guidelines (e.g., never log secrets or PII, always validate and sanitize inputs, follow OWASP recommendations)

**Example `.github/copilot-instructions.md`:**

```md
This repository targets .NET 9 and C# 13. Always use file-scoped namespaces.
Prefer primary constructors for simple dependency injection. Use the Result<T>
pattern from ErrorOr instead of throwing exceptions in application logic.
All tests use MSTest and NSubstitute for mocking.
```

> All technology choices shown in this guide (MSTest, NSubstitute, MediatR, ErrorOr, etc.) are illustrative examples based on one possible C# stack. Replace them with the testing, DI, error handling, and result libraries your team actually uses.

---

## Path-Specific Instructions

Path-specific instructions use `*.instructions.md` files placed under `.github/instructions/`. Create these as plain Markdown files in your repository and commit them — Copilot does not require any extra registration beyond the correct file path and valid frontmatter. Copilot only loads them from this directory — files placed anywhere else (e.g., `/docs`, `/config`, or the repo root) are silently ignored. Each file applies only when Copilot is assisting with files that match its configured glob pattern.

### File Structure

```
.github/
├── copilot-instructions.md          # Always loaded (repo-wide)
└── instructions/
    ├── src.instructions.md          # Applies to src/ files
    ├── tests.instructions.md        # Applies to test files
    ├── workflows.instructions.md    # Applies to GitHub Actions workflows
    └── api.instructions.md          # Any descriptive name you choose
```

> **Note:** Only the `.instructions.md` suffix is special — the `NAME` prefix is arbitrary and exists only for human readability. Names like `src.instructions.md` are conventions, not requirements.

> **Naming conventions:** Use lowercase, hyphenated names aligned to your architectural layers (e.g., `api.instructions.md`, `domain.instructions.md`, `infrastructure.instructions.md`, `tests.instructions.md`). Avoid overly granular names like `orders-controller.instructions.md` — one file per major layer or concern is the right level of granularity.

> **VS Code:** `.github/instructions` is the default location, but you can register additional instruction folders using the `github.copilot.chat.instructionsFilesLocations` setting if your repo layout requires a different structure.

### Frontmatter

Each `*.instructions.md` file must be valid Markdown with a valid YAML frontmatter block — invalid formatting or malformed YAML may cause Copilot to skip the file entirely with no error. The `applyTo` field controls which files trigger it. The `description` field is optional but useful for self-documentation. It is purely human-facing metadata — it does not change how or when Copilot applies the instructions.

For GitHub repository custom instructions, `applyTo` is required for a file to be applied automatically based on the file being edited. In VS Code custom instructions, you can omit `applyTo` and attach the instruction file manually in a chat session instead — useful for one-off or experimental instructions you don't want auto-applied.

```yaml
---
applyTo: "src/**/*.cs"
description: "C# source code conventions for the application layer"
---
```

You can specify multiple patterns in a single `applyTo` by separating them with commas. Note that `applyTo` expects a single string — do not use a YAML list, as it may not be recognized:

```yaml
# ✅ Correct — comma-separated string
applyTo: "src/**/*.cs,tests/**/*.cs"

# ❌ Incorrect — YAML list will not work
applyTo:
  - "src/**/*.cs"
  - "tests/**/*.cs"
```

This avoids creating multiple nearly-identical instruction files just to cover a few related patterns.

### Path Matching Glob Examples

Copilot uses Minimatch-style globbing, the same pattern syntax GitHub Actions uses for `paths`. This matters because Minimatch behaves differently from Bash globs or VS Code globs, particularly around `**`, dotfiles, and path separators. If a pattern works in a GitHub Actions `on.push.paths` filter, it will work here. Glob matching is case-sensitive in GitHub environments — ensure your `applyTo` patterns match the exact casing of your repository paths, which can catch developers on Windows where the local file system may be case-insensitive.

Patterns are evaluated relative to the repository root. Do not prefix them with `./` or `/` — patterns like `./src/**` or `/src/**` will not match.

`applyTo` does not support exclusion patterns. Even though Minimatch supports negation (e.g., `!src/Legacy/**`), Copilot's `applyTo` field accepts positive patterns only — exclusion patterns will silently fail to work as expected.

**Debugging a glob that isn't matching:** If you suspect an `applyTo` pattern is not matching the file you are editing, temporarily add an obviously wrong rule to that instruction file (e.g., "always add a comment saying GLOB TEST to every method"). Ask Copilot to generate code in a file that should match. If the test rule never appears in the output, your `applyTo` pattern is not matching — check for leading `./` or `/`, incorrect casing, or a path that doesn't align with the repo root. Remove the test rule once confirmed.

| Pattern | Matches |
|---|---|
| `src/**` | All files anywhere under `src/` |
| `**/*.cs` | All C# files in the entire repo |
| `.github/workflows/*.yml` | Only top-level workflow files |
| `tests/**/MSTest*.cs` | MSTest files anywhere under `tests/` |
| `src/**/Controllers/**` | Everything inside any Controllers folder |

### How Copilot Combines Instructions

Copilot combines repository-wide instructions with any matching path-specific instruction files as contextual guidance — there are no strict precedence rules determining which file takes priority. Instruction file order does not affect merging — Copilot blends all matching files regardless of filename or folder order. For the file `src/MyApi/Controllers/OrdersController.cs`, the following would be included:

- `.github/copilot-instructions.md` (always included for this repo)
- Any `*.instructions.md` whose `applyTo` glob matches the current file

**How merging works:**

- All matching files are blended together as combined context — there is no strict precedence system where a more specific file "wins" over a general one.
- Repo-wide instructions load first, followed by any matching path-specific files.
- If two files express contradictory rules (e.g., repo-wide says "use xUnit," test instructions say "use MSTest"), Copilot may follow either rule inconsistently depending on the prompt.
- Contradictions don't produce errors — they silently reduce output quality, which makes them easy to miss.

The practical takeaway: treat instruction files as complementary layers. Each file should cover ground the others don't.

---

## Prompt Files

Prompt files (`.prompt.md`) are distinct from instruction files. They are reusable prompts that must be manually triggered from the Copilot Chat interface — they do not load automatically or influence Copilot's behavior globally the way instruction files do. Note that prompt files are currently supported in Copilot Chat within VS Code and compatible environments — support in other IDEs such as Visual Studio or JetBrains riders may be limited or unavailable.

### File Structure

Prompt files must be placed under `.github/prompts/` for Copilot to recognize them — files placed elsewhere in the repo will not be discovered.

> **VS Code:** `.github/prompts` is the default workspace location, but you can register additional prompt folders using the `github.copilot.chat.promptFilesLocations` setting. You can also define user/profile-level prompt files that are available across all workspaces — useful for personal reusable prompts that don't belong in a specific repository. Think of repo prompt files as team-shared macros and profile prompt files as personal ones.

```
.github/prompts/
├── new-endpoint.prompt.md     # Scaffold a new API controller + service
├── add-tests.prompt.md        # Generate MSTest unit tests for a class
└── explain-arch.prompt.md     # Explain the architecture to a new developer
```

### Referencing Instruction Files

Prompt files can reference instruction files to pull in context:

```yaml
---
mode: "agent"
description: "Scaffold a new API endpoint with controller, service, and tests"
---
Scaffold a new REST endpoint following the conventions in
[src.instructions.md](../instructions/src.instructions.md).
```

When writing ad-hoc prompts or `.prompt.md` files, avoid telling Copilot to "ignore previous instructions" or to use styles that conflict with the repository's instruction files. Prompts and instruction files should point in the same direction — a prompt that fights the instructions produces inconsistent output and undermines the conventions the team has agreed on.

> **Note:** The frontmatter fields in `.prompt.md` files are part of Copilot Chat's own schema — they are not standard Git or YAML conventions. The `mode` field must be one of the supported values listed below; unsupported values will be ignored or cause unexpected behavior. These fields are interpreted entirely by the Copilot Chat integration, not by Git or any other tooling.

### Available Modes

| Mode | Behaviour |
|---|---|
| `ask` | Chat mode — Copilot answers but does not edit files |
| `edit` | Copilot edits files directly |
| `agent` | Full agentic mode, can run tools and make multi-file changes |

> These are the core modes currently supported. Additional or experimental modes may appear depending on your Copilot version — check the official docs for the latest. Note that `agent` mode availability depends on your IDE and Copilot subscription level.

---

## Enabling the Feature

In VS Code, path-specific instructions and prompt files require the feature to be explicitly enabled in your settings:

```json
{
  "github.copilot.chat.codeGeneration.useInstructionFiles": true
}
```

Other IDEs may enable instruction file support automatically or via a different setting — consult the Copilot documentation for your specific environment.

**User/profile-level instructions (VS Code only):** In addition to repository-scoped instruction files, VS Code supports defining instruction files at the profile or user level, making them available across all repositories. Use the `github.copilot.chat.instructionsFilesLocations` setting or the VS Code profile instructions folder to register additional locations. This is useful for personal conventions you want applied everywhere without committing them to individual repos.

---

## Suggested Instruction File Structure

Without a consistent structure, instruction files across a team tend to become freeform and hard to scan. The following template works well for both human readers and Copilot, since rules are clearly separated by intent:

```markdown
---
applyTo: "src/**/*.cs"
description: "Brief note on what this file covers"
---

## Context
What this layer or area of the codebase is responsible for, and any
architectural constraints Copilot should be aware of.

## Do
- Positive rules: patterns, conventions, and approaches to follow.
- Be specific — vague rules like "write clean code" add no value.

## Avoid
- Anti-patterns, banned libraries, or approaches that have been rejected.
- Explain the *why* where it isn't obvious (e.g., "avoid Moq — we standardised on NSubstitute").

## Examples
Short inline code snippets illustrating a key Do or Avoid where prose alone isn't clear.
Omit this section if the Do/Avoid rules are self-explanatory.
```

Not every section is required in every file — a `workflows.instructions.md` may only need **Do** and **Avoid**. The value is in the consistency across files, not rigid adherence to all four sections.

### Good vs. Bad Instructions

The most common failure is writing instructions that are too vague for Copilot to act on. Rules need to be specific and operational.

| ❌ Bad | ✅ Good |
|---|---|
| Write clean, maintainable code. | Use file-scoped namespaces. Prefer primary constructors for simple DI. |
| Follow best practices for error handling. | Use the `Result<T>` pattern from ErrorOr. Do not throw exceptions in application layer code. |
| Write good tests. | Test method names follow `MethodName_Scenario_ExpectedResult`. Each test has Arrange / Act / Assert sections marked with comments. |
| Use appropriate logging. | Use `ILogger<T>` injected via constructor. Log at `Information` for business events, `Warning` for recoverable errors, `Error` for unhandled exceptions. |
| Keep controllers thin. | Controllers must not contain business logic. Delegate to a service or MediatR handler immediately. |

The pattern: replace aspirational adjectives ("clean", "good", "appropriate") with concrete, verifiable rules.

### Instruction File Anti-Patterns

Structural problems that degrade Copilot's ability to follow instructions:

- **Long narrative prose** — dense paragraphs are harder for the model to parse than a direct bullet list.
- **Excessive inline commentary** — keep explanatory notes under each rule short. If a rule needs a long explanation, link to a design doc rather than embedding several paragraphs inline.
- **Mixing unrelated concerns in one file** — a file covering API conventions, database patterns, and logging simultaneously is hard to maintain and dilutes focus.
- **Embedding large code blocks** — short illustrative snippets are fine; pasting entire class implementations adds noise and bloat.
- **Duplicating rules across files** — the same rule in both `copilot-instructions.md` and `src.instructions.md` risks the two versions drifting apart and creates contradiction.
- **Contradictory rules** — even within a single file, rules that conflict (e.g., "always use async" and "prefer synchronous for simple methods") produce unpredictable output.

---

## Instruction Files and Your Codebase

Instruction files are most effective when they reinforce patterns already present in the codebase (see [When You Probably Don't Need Instruction Files](#when-you-probably-dont-need-instruction-files)). Copilot observes your existing code as part of its context — if instructions contradict what the code actually does, Copilot may follow the code rather than the instructions.

If your instructions describe an aspirational future state the codebase hasn't reached yet, update the code and the instructions together rather than letting them diverge. A gap between the two reduces consistency and can cause Copilot to ignore the instructions in favour of what it sees in the surrounding files.

---

## Instruction File Granularity

Finding the right level of granularity avoids both overly broad rules and fragmented, contradictory files.

**Guidelines:**

- **One file per major architectural layer or concern** — `api.instructions.md`, `domain.instructions.md`, `infrastructure.instructions.md`, `tests.instructions.md` is a reasonable default structure for a layered C# solution.
- **Avoid micro-files** — a separate instruction file per folder or feature (e.g., `orders.instructions.md`, `payments.instructions.md`) creates fragmentation and increases the risk of contradictions.
- **Merge files that are mostly identical** — if two files share 80% of their content, consolidate them using a comma-separated `applyTo` pattern.
- **Split files that cover multiple unrelated concerns** — if a single file has sections for API conventions, EF Core patterns, and logging, split it by concern so each file stays focused.

---

## Instruction File Testing Workflow

A repeatable process for validating instruction changes before they affect the whole team:

1. **Create a feature branch** for the instruction change.
2. **Modify the instruction file** and ensure the frontmatter is valid YAML.
3. **Open a file that matches the `applyTo` glob** to confirm the instruction is active (use the verification prompts in the Verifying section).
4. **Ask Copilot to generate a standard artifact** — a new controller, a unit test, a service class — that exercises the changed rule.
5. **Compare the output** against your expected conventions. Check specifically for the rule you changed.
6. **Repeat with your smoke test prompt set** to confirm no regressions in unrelated areas.
7. **Merge and document** the change in `CHANGELOG.md` or as an inline comment in the instruction file.

---

## Introducing Instruction Files to an Existing Repository

Adding instruction files to an established codebase is most effective when done incrementally. Trying to encode every convention on day one typically produces a bloated, contradictory file that degrades rather than improves output.

A practical rollout strategy:

1. **Audit your current conventions and tooling.** Identify what is already enforced by analyzers, linters, and CI — these don't need to be in instruction files. Focus on the gaps: conventions that matter but aren't mechanically enforced.
2. **Capture only high-value, non-enforced patterns first.** A small, focused repo-wide file covering your testing framework, error handling approach, and architecture rules is a better starting point than a comprehensive style guide.
3. **Add a repo-wide instruction file and test it.** Run a few standard generation prompts and check whether Copilot's output reflects the new rules before adding anything else.
4. **Introduce path-specific files gradually.** Add them when you notice Copilot generating code that diverges from layer-specific conventions — not preemptively.
5. **Revisit and refine.** After a sprint or two, review what's working and trim anything that isn't influencing output.

---

## Monorepo Considerations

Instruction files are repository-scoped — there is no support for separate `.github/instructions` directories per subproject within a monorepo. One instruction system applies across the entire repository.

**Partitioning rules across a multi-service or monorepo layout:**

- Use `.github/copilot-instructions.md` for truly global rules that apply everywhere — security guidelines, logging conventions, general coding style.
- Use path-scoped `*.instructions.md` files to express service- or package-specific conventions (e.g., `applyTo: "services/payments/**"`, `applyTo: "apps/web/**"`).
- Avoid duplicating global rules in service-specific files — keep shared rules in the repo-wide file and add only what is unique per area in the path-specific files. Duplication creates drift and contradictions.

In a monorepo containing multiple applications or packages, use granular `applyTo` patterns to target specific subprojects:

```yaml
applyTo: "apps/api/**/*.cs"       # Only the API application
applyTo: "apps/web/**/*.ts"       # Only the web frontend
applyTo: "packages/shared/**"     # Only the shared library
```

Avoid broad patterns like `**/*.cs` or `**/*.ts` if different subprojects follow different conventions — these cause cross-project rule conflicts where instructions intended for one package unintentionally influence another. Each instruction file should be scoped as narrowly as the conventions it describes.

---

## Practical Example: C# Repository Layout

```
.github/
├── copilot-instructions.md
├── instructions/
│   ├── src.instructions.md           # applyTo: src/**/*.cs
│   ├── tests.instructions.md         # applyTo: src/**/*.Tests/**/*.cs
│   ├── domain.instructions.md        # applyTo: src/**/Domain/**
│   └── workflows.instructions.md     # applyTo: .github/workflows/*.yml
└── prompts/
    ├── new-feature.prompt.md
    └── add-unit-tests.prompt.md
```

> **Note:** Test projects live under `src/` as MSTest `.csproj` projects within the solution, not in a separate `tests/` root folder. The `applyTo` glob targets test projects by naming convention — adjust it to match your own (e.g., `src/**/*.UnitTests/**/*.cs` or `src/**/*Tests*/**/*.cs`).

**Example `tests.instructions.md`:**

```yaml
---
applyTo: "src/**/*.Tests/**/*.cs"
description: "Test project conventions"
---
All test classes use MSTest (not xUnit). Use NSubstitute for mocking — never Moq.
Test method names follow the pattern: MethodName_Scenario_ExpectedResult.
Each test has clearly separated Arrange / Act / Assert sections marked with comments.
Prefer FluentAssertions for all assertions.
```

---

## Verifying That Instructions Are Active

Copilot doesn't display a confirmation when it loads instruction files, so it's worth knowing how to check manually.

**To verify which instructions are currently active:**

- Open Copilot Chat while editing a file and ask: *"What instructions are currently applied?"* Copilot will describe the instruction context it has loaded.
- Ask: *"Which instruction files are active right now?"* — this is useful when editing a file that should match a specific `applyTo` glob, and you want to confirm the match is working.
- In VS Code's Copilot Chat sidebar, look for a "Using instructions from…" indicator — this appears when Copilot has loaded one or more instruction files for the current context. Other clients may surface this information differently or not at all.

**Common reasons instructions are silently not loading:**

- The file is not under `.github/instructions/` (path-specific) or not named `.github/copilot-instructions.md` (repo-wide)
- The `applyTo` glob does not match the file you are editing — test your glob against your actual file paths
- The `github.copilot.chat.codeGeneration.useInstructionFiles` setting is not enabled in VS Code
- The frontmatter is malformed — YAML is whitespace-sensitive and a missing quote or indent will silently break parsing

**Debugging conflicts:**

If output becomes inconsistent after adding a new instruction file, temporarily comment out or disable recently added rules to isolate the conflict. Re-enable rules one at a time until the inconsistency reappears — that rule is either contradicting another file or too vague to be applied reliably.

---

## Instruction File Size and Signal-to-Noise Ratio

Copilot loads instruction files as context alongside your code and prompt. Critically, everything — your current file, related files, your prompt, and all instruction files — must fit into a single context window. The available context window varies by model and Copilot version, but the principle remains the same: oversized instruction files compete directly with relevant code for attention. The longer and noisier that context is, the harder it is for the model to identify and follow the rules that actually matter. Conciseness is not a style preference — it directly affects output quality.

**Guidelines:**

- Keep repo-wide instructions under roughly 200–300 lines. Beyond that, important rules get diluted by surrounding content.
- Apply the same brevity principle to path-specific files. If a single `*.instructions.md` grows beyond a few hundred lines, consider splitting it by concern (e.g., `api.instructions.md`, `persistence.instructions.md`) to keep each file focused and scannable.
- Write rules as bullet points, not paragraphs. Dense prose is harder for the model to parse cleanly than a direct, scannable list.
- Avoid duplicating rules across files. If the same rule appears in both `copilot-instructions.md` and `src.instructions.md`, it adds noise and risks the two versions drifting out of sync.
- Remove rules that are no longer relevant. Stale instructions are just noise — a rule that no longer reflects the codebase may actively mislead Copilot.

---

## Security and Sensitive Data

Instruction files are committed to source control and are visible to everyone with access to the repository. Treat them accordingly.

- **Do not include secrets, tokens, API keys, or credentials** of any kind — use environment variables or a secrets manager instead.
- **Do not include sensitive internal details** such as internal service URLs, proprietary business logic, or security-sensitive architecture details that should not be broadly visible.
- **Do not include personal data** about users, customers, or colleagues.
- **Avoid pasting large third-party code snippets or Q&A answers** (e.g., from Stack Overflow) into instruction files. They add noise, go out of date quickly, and may introduce licensing concerns. Summarize the rule in your own words and link to the source instead.
- If your repo is public, assume instruction files are fully public. Even in private repos, they are visible to all collaborators.

---

## Versioning and Evolution of Instructions

Instruction files directly shape Copilot's output across the whole team. A careless change can silently alter generated code for everyone. Treat them with the same discipline as code.

- **Require PR review** for all changes to instruction files — at minimum one reviewer who understands the codebase conventions being expressed.
- **Test changes on a feature branch** before merging. Edit a file that matches the relevant `applyTo` glob and run a few standard prompts to confirm the new rules produce the expected output.
- **Document significant changes** in a `CHANGELOG.md` or inline comment so the team understands why a rule was added, changed, or removed.
- **Delete stale rules** rather than leaving them in place. An outdated instruction is actively harmful — it contradicts current practice and degrades output quality.
- **Be aware of branch state** — Copilot uses the instruction files from the currently checked-out branch. If conventions differ across long-lived branches, generated code may differ as well. Teams maintaining divergent long-lived branches should ensure each branch's instruction files reflect that branch's conventions.
- **Maintain a lifecycle discipline** — instruction files can become orphaned as the codebase evolves:
  - When a folder is renamed, update the `applyTo` glob to match the new path.
  - When an architectural layer is removed, delete its instruction file.
  - When conventions change, update both the code and the instructions together — a gap between the two reduces effectiveness.

---

## Tips

- Keep repo-wide instructions concise. Long files reduce signal-to-noise and dilute the rules that actually matter. Avoid copying in design documents, architecture overviews, or API references — these overwhelm the instruction context. Link to them instead (e.g., "see `docs/architecture.md` for layer definitions").
- Use path-specific files to capture context that only matters in one layer (e.g., EF Core conventions only in the Infrastructure layer).
- Don't copy your entire linter or formatter config into instructions. Rules already enforced by `.editorconfig`, ESLint, or Roslyn analyzers don't need to be repeated — Copilot seeing them adds noise without benefit. Instead, highlight only the rules that meaningfully affect code structure or design choices that tooling can't enforce on its own.
- Commit instruction files to source control so the whole team benefits.
- Prompt files are good candidates for onboarding tasks like "explain this architecture" or repetitive scaffolding workflows.
- Re-evaluate instructions periodically as the codebase evolves. When you change an instruction file, run a few standard prompts (e.g., "write a new test", "add a controller") to confirm Copilot follows the updated rules — it's easy for an edit to introduce a contradiction that silently degrades output quality.
- Keep a small set of standard "smoke test" prompts in a shared doc (e.g., "Generate a new controller + service," "Write a unit test for this class," "Add logging to this method") and rerun them after significant instruction changes to quickly spot regressions.

---

## References

- [About GitHub Copilot Instructions – GitHub Docs](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot)
- [SebastienDegodez/copilot-instructions](https://github.com/SebastienDegodez/copilot-instructions)
- [Robotti-io/copilot-security-instructions](https://github.com/Robotti-io/copilot-security-instructions)