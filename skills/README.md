# Skills

The [Agent Skills](https://agentskills.io/) format was originally developed by [Anthropic](https://www.anthropic.com/), released as an open standard, and has been adopted by a growing number of agent products. The standard is open to contributions from the broader ecosystem.

## Agent Skills (`.github/skills` or `.claude/skills`)

- **Purpose:** Encapsulate **specific, reusable capabilities** that agents can call on-demand.    
- **Use case:** Task-oriented, modular logic that can be shared across agents and repositories.
- **Behavior:** Skills are **invoked automatically** by the agent if relevant or can be explicitly invoked in Copilot CLI. They are portable.

**Rule of thumb:**

> Use skills for **task-specific logic** that can be reused in multiple contexts.

**Example:**

- `github-actions-failure-debugging` skill that knows how to parse logs and suggest fixes.    
- `webapp-testing` skill that runs integration tests and reports issues.    


## Online Resources

- [coreyhaines31/marketingskills](https://github.com/coreyhaines31/marketingskills)
- [atlassian-skills/langping](https://github.com/langpingxue/atlassian-skills/) - local Skills with Python scripts to interact with API endpoints. [Introducing Atlassian Skills: Extending AI agent with Atlassian Integration](https://medium.com/@xuelangping/introducing-atlassian-skills-extending-ai-agent-with-atlassian-integration-fa19f6056df7) provides more background.
- [claude-skills/alirezarezvani](https://github.com/alirezarezvani/claude-skills) - library of skills organized by persona
-  [anthropics skills](https://github.com/anthropics/skills)
- [github/awesome-copilot](https://github.com/github/awesome-copilot)


