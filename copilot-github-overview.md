









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


