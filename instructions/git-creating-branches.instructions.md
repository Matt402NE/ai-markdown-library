---
applyTo: ".github/**"
description: "Rules for creating local Git branches following the team naming convention."
---

## Context

This covers the workflow for creating new local Git branches from `main`. Branches follow a strict `{type}/{author}/{description}` naming convention defined in `.github/instructions/references/branch-naming-conventions.md`. This skill is scoped to branch creation only — it does not cover commits, merges, rebases, or post-merge cleanup.

## Do

- Always ask the user for their **author identifier** (initials like `jd` or hyphenated full name like `john-doe`, lowercase) at the start of every session. Never assume or reuse a value from a prior conversation.
- Always ask whether a ticket reference exists before proposing branch names. Use `issue-###` format (e.g., `issue-432`) when one exists. Use `no-ref` only when the user explicitly confirms there is no ticket.
- Run pre-flight checks in this exact order before creating a branch:
  1. Verify inside a git repository: `git rev-parse --is-inside-work-tree`
  2. Confirm on `main`: `git branch --show-current`
  3. Pull latest: `git pull origin main`
  4. Confirm clean working tree: `git status --porcelain` (must be empty)
- Stop and report to the user on the **first** pre-flight failure. Do not continue past a failure.
- Propose **2–3 branch name options** that vary only in description phrasing. Validate each against the naming rules before presenting them.
- Check that no proposed name already exists: `git branch --list "{proposed-name}"`
- Show a dry-run of the exact command before executing: `git checkout -b {approved-branch-name}`
- Wait for explicit user confirmation before executing the branch creation command.
- Confirm success after creation: `git branch --show-current`

## Avoid

- Never use `git worktree` — the team does not use worktrees.
- Never switch branches automatically if the user is not on `main` — inform the user and stop.
- Never stash or commit uncommitted changes to resolve a dirty working tree — inform the user and stop.
- Never proceed with branch creation without explicit user approval of the proposed name and the dry-run command.
- Never reuse an author identifier from a previous conversation — always ask. Author identifiers are session-specific input, not stored state.
- Never use generic or vague description segments such as `update`, `changes`, or `stuff` — descriptions must be specific to the work being done.
- Never use uppercase letters, spaces, or underscores anywhere in a branch name — all characters must be lowercase alphanumeric, hyphens, or forward slashes (dots allowed only in release version numbers).
- Never omit the ticket reference segment without the user confirming `no-ref` applies.

## Examples

Valid branch names:
```
feature/jd/issue-456-user-authentication
bugfix/sarah-jones/issue-789-fix-login-redirect
hotfix/mk/no-ref-critical-payment-bug
release/js/no-ref-v2.1.0
docs/ab/issue-234-update-api-documentation
test/jd/no-ref-redis-caching-experiment
```

Invalid — and why:
```
feature/new-stuff          ← missing author, vague description
Feature/JD/add_login       ← uppercase, underscores
fix-bug                    ← missing author and type/author/description structure
```
