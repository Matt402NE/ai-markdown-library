# Copilot Instruction Files

This folder contains path-specific instruction files that customize GitHub Copilot's behavior for different areas of the codebase.

---

## How It Works

Copilot automatically loads two types of instruction files:

|File|When it loads|
|---|---|
|`.github/copilot-instructions.md`|Always — for every file in this repo|
|`.github/instructions/*.instructions.md`|Only when editing files that match the `applyTo` glob|

All matching files are **blended together as context** — there is no override precedence. Contradictions between files produce inconsistent output.

---

## File Naming

Only the `.instructions.md` suffix matters. The prefix is arbitrary — use lowercase, hyphenated names aligned to your architectural layers:

```
src.instructions.md           # applyTo: src/**/*.cs
tests.instructions.md         # applyTo: src/**/*.Tests/**/*.cs
domain.instructions.md        # applyTo: src/**/Domain/**
workflows.instructions.md     # applyTo: .github/workflows/*.yml
```

---

## Frontmatter

Every file must have valid YAML frontmatter. Invalid YAML causes Copilot to silently skip the file.

```yaml
---
applyTo: "src/**/*.cs"
description: "Human-readable note — does not affect Copilot behavior"
---
```

- `applyTo` uses Minimatch-style globs (same as GitHub Actions `paths`).
- Patterns are relative to the repo root — do not prefix with `./` or `/`.
- Multiple patterns: `applyTo: "src/**/*.cs,tests/**/*.cs"` — comma-separated string, **not** a YAML list.
- Exclusion patterns (e.g. `!src/Legacy/**`) are **not supported**.

---

## What These Files Should Contain

Instructions work best when they are **short, specific, and additive** to what tooling already enforces.

**Do include:**

- Conventions Copilot can't infer from existing tooling (architecture rules, pattern choices, library preferences)
- Concise bullet points — not paragraphs

**Do not include:**

- Rules already enforced by `.editorconfig`, Roslyn analyzers, or CI
- Secrets, tokens, internal URLs, or sensitive details
- Large code blocks, design docs, or copied Q&A answers

---

## Instructions Are Advisory

Copilot instructions **influence** generation — they do not enforce, validate, or guarantee compliance. Generated code should still be reviewed. Instructions only affect future generations; they do not retroactively modify existing code.

---

## Keeping These Files Healthy

- **Changing an instruction file?** Test on a branch first — run a few standard prompts to confirm the change produces the expected output.
- **Renaming a folder?** Update the `applyTo` glob to match.
- **Removing an architectural layer?** Delete its instruction file.
- **Instruction files require PR review** — a careless change silently affects Copilot output for the whole team.

---

## If Instructions Aren't Loading

- Confirm the file is under `.github/instructions/` (not `/docs`, root, etc.)
- Confirm the `applyTo` glob matches the file you're editing (no leading `./` or `/`, correct casing)
- In VS Code, confirm `github.copilot.chat.codeGeneration.useInstructionFiles` is `true`
- Ask Copilot: _"Which instruction files are active right now?"_

---

> For the full guide including examples, anti-patterns, monorepo guidance, and a migration strategy, see [`copilot-instructions-guide.md`](copilot-instructions-guide.md).