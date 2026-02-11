# Instructions

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


## Online Resources

* [SebastienDegodez/copilot-instructions](https://github.com/SebastienDegodez/copilot-instructions)
* [Robotti-io/copilot-security-instructions](https://github.com/Robotti-io/copilot-security-instructions)