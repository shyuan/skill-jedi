# Skill Design Patterns

Proven patterns for writing effective Claude Code skills. Based on obra/superpowers ecosystem research, Anthropic documentation, and community best practices (updated 2026-03).

---

## Frontmatter Reference

### Required Fields

| Field | Rules | Notes |
|-------|-------|-------|
| `name` | Letters, numbers, hyphens only; max 64 chars | If omitted, uses directory name |
| `description` | Max 1024 chars; third person; `Use when...` format | Claude uses this to decide whether to invoke |

### Optional Fields

| Field | Type | Purpose |
|-------|------|---------|
| `argument-hint` | string | Autocomplete hint, e.g. `[issue-number]` or `[filename] [format]` |
| `context` | `fork` | Run in isolated subagent context (no conversation history) |
| `agent` | string | Subagent type when `context: fork` — `Explore`, `Plan`, `general-purpose` |
| `model` | string | Override model for this skill |
| `allowed-tools` | list | Tools Claude can use without permission when skill is active |
| `hooks` | object | Lifecycle hooks scoped to this skill |
| `disable-model-invocation` | bool | `true` = only user can `/invoke` (hidden from Claude) |
| `user-invocable` | bool | `false` = only Claude can auto-invoke (hidden from `/` menu) |

### Invocation Control Matrix

| Setting | User invokes | Claude invokes | Description loaded at startup |
|---------|-------------|----------------|-------------------------------|
| Default | Yes | Yes | Yes |
| `disable-model-invocation: true` | Yes | No | No |
| `user-invocable: false` | No | Yes | Yes |

### Frontmatter Principles

- **Description = trigger condition**, not functionality description
- Start with `Use when...`, write in third person
- **Never describe workflow steps in description** — AI may read only the description and act on it prematurely
- Include keywords users would naturally say (improves auto-invocation matching)
- Description budget is 2% of context window, shared across all skills (1M context ≈ 20K chars total). Be concise, but prioritize trigger quality over arbitrary length limits.

---

## Dynamic Content Injection

### String Substitutions

```yaml
---
name: fix-issue
argument-hint: "[issue-number]"
---

Fix GitHub issue $ARGUMENTS following our coding standards.
Use $0 as the issue number.
Session: ${CLAUDE_SESSION_ID}
Skill dir: ${CLAUDE_SKILL_DIR}
```

| Variable | Resolves to |
|----------|-------------|
| `$ARGUMENTS` | All arguments after `/skill-name` |
| `$0`, `$1`, `$2` | Individual positional arguments |
| `$ARGUMENTS[0]` | Alternative syntax for positional |
| `${CLAUDE_SESSION_ID}` | Current session ID |
| `${CLAUDE_SKILL_DIR}` | Absolute path to skill directory |

### Shell Command Injection

Pre-render skill content with live data using the `!` + backtick-wrapped command syntax:

```markdown
## Current PR context
- Diff: !`gh pr diff`
- Comments: !`gh pr view --comments`

Review this PR based on our standards.
```

The command executes **before** Claude sees the skill content. Output replaces the placeholder inline. Use for injecting dynamic context (git state, PR info, env data).

---

## Context Modes

### Inline (default)

Skill runs in the main conversation. Has access to full conversation history. Best for reference material, guidelines, and skills that need conversational context.

### Forked (`context: fork`)

Skill runs in an isolated subagent. No conversation history. Best for:
- Scoped tasks with explicit, self-contained instructions
- Heavy operations that shouldn't pollute main context
- Tasks that benefit from a specific agent type

```yaml
---
name: deep-review
context: fork
agent: Explore
---

Thoroughly analyze the codebase for security issues...
```

---

## Content Structure Template

```
Overview        → Core principle in 1-2 sentences
When to Use     → Symptom/scenario list + when NOT to use
Core Pattern    → Decision flow or key rules
Quick Reference → Scannable lookup table
Implementation  → Concrete steps
Common Mistakes → Frequent errors + corrections
```

Keep the structure flat. Readers (AI and human) scan top-down. Put the most important information first.

---

## Six Key Patterns

### 1. Iron Law

A single, absolute rule placed in a code block. No exceptions, no qualifiers.

```
NEVER skip the confirmation step before destructive operations.
```

Works because it removes ambiguity. AI has no room to interpret or negotiate.

### 2. Rationalization Table

Pre-empt the ways AI will justify skipping your rules. Format as a two-column table:

| AI thinks... | Correct response |
|---|---|
| "This is a simple case, I can skip validation" | Always validate. Simple cases become complex. |
| "The user seems to want speed over safety" | Safety is non-negotiable unless explicitly overridden. |
| "I already checked this in a previous step" | Re-check. Context may have changed. |

This is the most underrated pattern. Without it, AI will find creative reasons to bypass your carefully written rules.

### 3. Red Flags

Self-check triggers. When AI notices itself thinking certain thoughts, it should pause and reconsider.

> **Red flags** — If you catch yourself thinking any of these, STOP:
> - "This is probably fine..."
> - "I'll clean this up later..."
> - "The user won't notice..."

### 4. Phase-Gate

For complex workflows, define sequential phases. Each phase must complete before the next begins.

```
Phase 1: Research → Read all relevant files. Do NOT write code yet.
Phase 2: Plan    → Present approach to user. Wait for approval.
Phase 3: Execute → Implement the approved plan.
Phase 4: Verify  → Run tests. Confirm no regressions.
```

Prevents the AI from jumping ahead to implementation before understanding the problem.

### 5. Hub-and-Spoke

SKILL.md is the hub — concise, scannable, always loaded. Detailed references live in `references/` and are loaded on demand.

```
skills/my-skill/
├── SKILL.md              # < 200 words, links to references
└── references/
    ├── api-guide.md      # loaded when AI needs API details
    └── error-catalog.md  # loaded when debugging
```

Rules:
- References only one level deep (no nested references)
- Never use `@` links (force-loads entire file into context)
- SKILL.md should be useful even without loading references

This pattern is now the **official recommended architecture** — Claude Code's progressive loading system is designed around it: startup loads name+description, invocation loads SKILL.md, references load on demand.

### 6. Concrete Verification

Show correct and incorrect examples side by side. AI learns faster from contrast than from description.

```markdown
## Wrong
git add -A && git commit -m "fix"

## Right
git add src/specific-file.ts
git commit -m "fix: resolve null check in user validation"
```

---

## Persuasion Principles

Based on Meincke et al. (2025) — which influence techniques work on LLMs:

| Principle | How to apply | Example |
|---|---|---|
| **Authority** | `YOU MUST` / `Never` / `No exceptions` | Most effective for disciplinary skills |
| **Commitment** | Require explicit declaration of choice | "State which approach you chose and why" |
| **Scarcity** | Time/order constraints | "Before proceeding, verify X" |
| **Social Proof** | Universal patterns, failure modes | "Every successful deployment includes..." |

**Avoid**:
- **Reciprocity** — feels manipulative, AI may over-comply
- **Liking** — produces sycophantic behavior

---

## Skill Types

### Technique
Concrete method with specific steps. Best for repeatable workflows.
Example: `condition-based-waiting` — how to wait for async conditions.

### Pattern
A thinking approach, not a step-by-step procedure. Best for judgment calls.
Example: `flatten-with-flags` — when to flatten nested conditionals.

### Reference
API documentation, syntax guides, lookup tables. Best for factual knowledge.
Example: `office-docs` — Microsoft Office XML format reference.

---

## Token Efficiency

### Budget Mechanics

- **Description budget**: 2% of context window, shared across all skills. If you have many skills, low-priority descriptions get excluded.
- Override with `SLASH_COMMAND_TOOL_CHAR_BUDGET` environment variable
- Fallback: 16,000 characters total for all descriptions

### Guidelines

| Component | Budget |
|-----------|--------|
| Description | Concise but complete — prioritize trigger quality (budget: 2% of context window) |
| SKILL.md body | < 500 words, < 500 lines |
| Frequently loaded skills | < 200 words body |
| References | One level deep, loaded on demand |

### Progressive Loading (Official)

```
Startup     → name + description only (always in context)
Invocation  → SKILL.md body loaded
On demand   → references/ files loaded as needed
```

This is not a suggestion — it's how the system works. Design your skill to be useful at each level.

---

## TDD Development Process

### RED — Establish Baseline

1. Create a realistic pressure scenario where AI typically fails
2. Run the scenario **without** the skill
3. Record exact failure behavior (screenshots, logs, transcripts)
4. This is your regression test

### GREEN — Write and Validate

1. Write the skill following the patterns above
2. Run the same scenario **with** the skill loaded
3. Verify the behavior improves
4. Check token cost — is the skill worth the context it consumes?

### REFACTOR — Harden

1. Run more scenarios. Find new ways AI rationalizes bypassing your rules
2. Add these to the Rationalization Table
3. Trim unnecessary words — every token costs context
4. Retest to ensure changes don't regress behavior

This cycle continues. Good skills are maintained, not just written.

---

## Skill-Scoped Hooks

Skills can define lifecycle hooks in frontmatter:

```yaml
---
name: deploy
hooks:
  PostToolUse:
    - matcher: "Bash(npm *)"
      hooks:
        - type: command
          command: "npm audit"
---
```

Hook events include `PreToolUse` (can block), `PostToolUse`, `Elicitation`, `PostCompact`, and 13 others. Use hooks for safety gates, auto-validation, and workflow enforcement within a skill's scope.
