---
name: skill-review
user_invocable: true
description: >
  Audit and review Claude Code skills for quality, anti-patterns, and effectiveness.
  Use when reviewing an existing skill's design, checking for common mistakes,
  running a quality audit before publishing, or when a skill isn't triggering
  or behaving as expected. Also use when the user says "review my skill" or
  "why isn't my skill working".
---

# Skill Review — Anti-Pattern Audit

## How to Use

1. Read the skill being reviewed (SKILL.md + references)
2. Run through the checklist below
3. For any failing item, consult [references/anti-patterns.md](references/anti-patterns.md) for the full explanation and fix

## Quick Checklist

### Trigger & Matching
- [ ] Description is a trigger condition, not a workflow summary
- [ ] Description uses third person, `Use when...` format
- [ ] Description includes keywords users would naturally say
- [ ] Description doesn't overlap with other installed skills
- [ ] Tested with 5+ trigger prompts including false triggers

### Content Design
- [ ] SKILL.md < 500 lines, body < 500 words
- [ ] One clear responsibility (not a God Skill)
- [ ] Directives, not suggestions ("Always", not "Consider")
- [ ] No basic concept explanations Claude already knows
- [ ] Decision frameworks, not rigid step-by-step procedures
- [ ] Has a Gotchas section built from real failure modes

### Structure & References
- [ ] References linked from decision points (no orphans)
- [ ] No nested references; no `@` links
- [ ] No duplicated content across files

### Rule Enforcement
- [ ] Every Iron Law has a companion Rationalization Table
- [ ] Critical rules repeated at decision points, not just at top

### Security & Operations
- [ ] `allowed-tools` is minimal (principle of least privilege)
- [ ] No executable scripts unless audited
- [ ] No `!` in inline code spans (parser breakage)

### Effectiveness
- [ ] Skill tested with TDD workflow (RED → GREEN → REFACTOR)
- [ ] Gotchas section exists and is maintained over time
- [ ] Token cost justified by behavior improvement

## Full Anti-Pattern Catalog

22 documented anti-patterns with symptoms, root causes, and fixes — see [references/anti-patterns.md](references/anti-patterns.md).
