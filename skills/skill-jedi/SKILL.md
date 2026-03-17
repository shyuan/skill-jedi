---
name: skill-jedi
description: >
  Guide for designing effective Claude Code skills and plugins with proven patterns.
  Use when creating a new skill and need design guidance beyond basic structure,
  when deciding skill architecture (MCP vs skill vs plugin), when optimizing
  token efficiency, or when packaging skills into plugins for marketplace distribution.
  Also use when reviewing or improving an existing skill's design quality.
---

# Skill Jedi — Design Guide for Claude Code Skills & Plugins

## Core Philosophy

> "MCP provides *capability*, skill provides *judgment*."

MCP tools give Claude new abilities (API calls, file ops). Skills shape *how* Claude thinks and decides. A well-designed skill changes behavior, not just adds features.

## Architecture Decision

| Need | Solution | Complexity |
|------|----------|------------|
| Teach Claude a workflow or pattern | Skill only | Low |
| Give Claude new capabilities + guidance | MCP server + Skill | Medium |
| Distribute to other users | Plugin (three-repo) | High |

## Frontmatter Quick Reference

Required: `name`, `description`. Key optional fields:

| Field | Purpose |
|-------|---------|
| `argument-hint` | Autocomplete hint, e.g. `[issue-number]` |
| `context: fork` | Run in isolated subagent (no conversation history) |
| `agent` | Subagent type for forked context (`Explore`, `Plan`, etc.) |
| `model` | Override model for this skill |
| `allowed-tools` | Tools that skip permission prompt |
| `hooks` | Skill-scoped lifecycle hooks |
| `disable-model-invocation` | Only user can `/invoke` |
| `user-invocable: false` | Only Claude auto-invokes |

Full details in [references/design-patterns.md](references/design-patterns.md).

## Dynamic Content

Skills support runtime injection:

- **Arguments**: `$ARGUMENTS`, `$0`, `$1` — from `/skill-name arg0 arg1`
- **Variables**: `${CLAUDE_SESSION_ID}`, `${CLAUDE_SKILL_DIR}`
- **Shell**: `` !`gh pr diff` `` — executes before Claude sees the skill

## Six Design Patterns

Quick reference — details in [references/design-patterns.md](references/design-patterns.md):

1. **Iron Law** — One absolute rule in a code block. No exceptions.
2. **Rationalization Table** — Pre-empt AI self-justification for skipping rules.
3. **Red Flags** — Trigger self-check when AI detects risky thought patterns.
4. **Phase-Gate** — Sequential stages; must complete each before proceeding.
5. **Hub-and-Spoke** — SKILL.md as concise hub; details in references/.
6. **Concrete Verification** — Show correct vs incorrect examples side by side.

## Token Budget

- Description budget: **2% of context window** (dynamic); keep under 200 chars
- SKILL.md body: < 500 words, < 500 lines
- References: one level deep, loaded on demand — never use `@` links
- Progressive loading: startup = name+description only → invocation = SKILL.md → on-demand = references/

## Anti-Patterns

19 common mistakes that degrade skill quality — [references/anti-patterns.md](references/anti-patterns.md). Includes a review checklist for auditing existing skills.

## TDD Workflow

**RED**: Build a pressure scenario. Test without the skill. Record baseline.
**GREEN**: Write the skill. Test with it. Verify improvement.
**REFACTOR**: Find new rationalizations AI uses to bypass rules. Add to rationalization table. Retest.

## Plugin Packaging

Three-repo architecture for distribution — details in [references/plugin-architecture.md](references/plugin-architecture.md):

- **Marketplace repo** — Plugin catalog only, minimal
- **Plugin repo** — Skills + embedded MCP dist, the installable unit
- **MCP server repo** — Standalone server, usable outside Claude Code

Plugins now support: skills, commands, agents, hooks, MCP servers, LSP servers, output styles.
