# Agents

## Custom Agents

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


## Online Resources

- [dfinke/awesome-copilot-chatmodes](https://github.com/dfinke/awesome-copilot-chatmodes)
-  [anthropics skills](https://github.com/anthropics/skills)
- [github/awesome-copilot](https://github.com/github/awesome-copilot)
