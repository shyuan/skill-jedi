# skill-jedi

Design patterns and architecture guide for building effective Claude Code skills and plugins.

Complements the built-in `skill-creator` (which teaches *how to build*) by teaching **how to design well**.

## Three Skills

| Command | Purpose |
|---------|---------|
| `/skill-jedi` | Skill design guide — patterns, types, frontmatter, token budget, TDD |
| `/skill-review` | Audit an existing skill against 22 anti-patterns |
| `/plugin-guide` | Package skills into plugins for marketplace distribution |

## What's inside

- **Nine skill types** — Library/API Reference, Product Verification, Data Fetching, Business Process, Code Scaffolding, Code Quality, CI/CD, Runbooks, Infrastructure Ops
- **Six design patterns** — Iron Law, Rationalization Table, Red Flags, Phase-Gate, Hub-and-Spoke, Concrete Verification
- **Best practices** — Gotchas sections, setup/config pattern, scripts & code composition, skill composition, persistent data with `${CLAUDE_PLUGIN_DATA}`
- **Complete frontmatter reference** — All fields including `context: fork`, `argument-hint`, `hooks`, invocation control
- **Dynamic content injection** — `$ARGUMENTS`, `${CLAUDE_SKILL_DIR}`, shell command pre-rendering
- **Token budget mechanics** — 2% context window budget, progressive loading system
- **22 anti-patterns** — Common mistakes with symptoms, fixes, and a review checklist
- **Plugin packaging** — Full plugin.json schema, 6 source types, marketplace curation, skill composition
- **Hooks system** — 17 lifecycle events, 3 handler types, skill-scoped & on-demand hooks
- **TDD workflow** — RED/GREEN/REFACTOR cycle for skill development

## Install

```bash
# Add as a dev marketplace in Claude Code
/plugin marketplace add shyuan/skill-jedi
```

## Usage

In any Claude Code session:

```
/skill-jedi        # design guidance
/skill-review      # audit a skill
/plugin-guide      # packaging & distribution
```

## License

MIT
