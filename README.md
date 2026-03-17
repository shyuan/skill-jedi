# skill-jedi

Design patterns and architecture guide for building effective Claude Code skills and plugins.

Complements the built-in `skill-creator` (which teaches *how to build*) by teaching **how to design well**.

## What's inside

- **Six design patterns** — Iron Law, Rationalization Table, Red Flags, Phase-Gate, Hub-and-Spoke, Concrete Verification
- **Complete frontmatter reference** — All fields including `context: fork`, `argument-hint`, `hooks`, invocation control
- **Dynamic content injection** — `$ARGUMENTS`, `${CLAUDE_SKILL_DIR}`, shell command pre-rendering
- **Context modes** — Inline vs forked execution, subagent type selection
- **Architecture decisions** — When to use skill-only vs MCP+skill vs full plugin
- **Token budget mechanics** — 2% context window budget, progressive loading system
- **Plugin packaging** — Full plugin.json schema, 6 source types, hooks, LSP servers, release channels
- **Hooks system** — 17 lifecycle events, 3 handler types, skill-scoped hooks
- **TDD workflow** — RED/GREEN/REFACTOR cycle for skill development

## Install

```bash
# Add as a dev marketplace in Claude Code
/plugin marketplace add shyuan/skill-jedi
```

## Usage

In any Claude Code session:

```
/skill-jedi
```

Then ask questions like:
- "I want to create a skill plugin for my project"
- "Should I use MCP or a skill for this?"
- "How do I structure my SKILL.md?"

## License

MIT
