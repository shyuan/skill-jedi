# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

skill-jedi is a Claude Code plugin that teaches **skill and plugin design patterns**. It complements the built-in `skill-creator` (which teaches how to build) by teaching how to design well. It is a content-only project — no build system, no tests, no dependencies.

## Repository Structure

- `.claude-plugin/` — Plugin metadata (`plugin.json`, `marketplace.json`)
- `skills/` — Three independently invocable skills:
  - `skill-jedi/` — Skill design guide (`/skill-jedi`)
    - `SKILL.md` — Hub: patterns, types, frontmatter, token budget
    - `references/design-patterns.md` — Full pattern details, nine skill types, best practices
  - `skill-review/` — Skill quality audit (`/skill-review`)
    - `SKILL.md` — Hub: review checklist
    - `references/anti-patterns.md` — 22 anti-patterns with fixes
  - `plugin-guide/` — Plugin packaging guide (`/plugin-guide`)
    - `SKILL.md` — Hub: distribution, composition, measuring
    - `references/plugin-architecture.md` — Three-repo model, plugin.json schema, hooks

Each skill follows the **Hub-and-Spoke** pattern: SKILL.md is a concise hub; details live in `references/`.

## Key Concepts for Editing

- **Token budget matters**: Each SKILL.md body should stay < 500 words / < 500 lines. Descriptions share a 2% context-window budget across all skills.
- **Progressive loading**: startup loads only `name`+`description` → invocation loads SKILL.md → references load on demand. Never use `@` links in references.
- **References are one level deep**: no nested subdirectories under `references/`.
- **One skill = one job**: skill-jedi (design), skill-review (audit), plugin-guide (distribution).

## Installation (for testing locally)

```bash
/plugin marketplace add shyuan/skill-jedi
```

Then invoke with `/skill-jedi`, `/skill-review`, or `/plugin-guide` in any Claude Code session.
