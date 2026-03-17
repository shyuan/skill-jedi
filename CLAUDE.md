# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

skill-jedi is a Claude Code plugin that teaches **skill and plugin design patterns**. It complements the built-in `skill-creator` (which teaches how to build) by teaching how to design well. It is a content-only project — no build system, no tests, no dependencies.

## Repository Structure

- `.claude-plugin/` — Plugin metadata (`plugin.json`, `marketplace.json`)
- `skills/skill-jedi/SKILL.md` — Main skill file (the hub), loaded when `/skill-jedi` is invoked
- `skills/skill-jedi/references/` — On-demand reference docs loaded progressively:
  - `design-patterns.md` — Six patterns, frontmatter spec, TDD workflow
  - `plugin-architecture.md` — Three-repo model, plugin.json schema, hooks system
  - `anti-patterns.md` — 19 common mistakes with review checklist

This follows the **Hub-and-Spoke** pattern: SKILL.md is a concise hub; details live in `references/`.

## Key Concepts for Editing

- **Token budget matters**: SKILL.md body should stay < 500 words / < 500 lines. Descriptions share a 2% context-window budget across all skills.
- **Progressive loading**: startup loads only `name`+`description` → invocation loads SKILL.md → references load on demand. Never use `@` links in references.
- **References are one level deep**: no nested subdirectories under `references/`.

## Installation (for testing locally)

```bash
/plugin marketplace add shyuan/skill-jedi
```

Then invoke with `/skill-jedi` in any Claude Code session.
