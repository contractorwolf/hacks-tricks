# Claude Code Skills Guide

Skills are reusable workflows and knowledge that extend Claude Code's capabilities. They're invoked via slash commands (e.g., `/my-skill`) or automatically when Claude detects they're relevant.

---

## What Are Skills?

Skills let you:
- **Inject knowledge** — Teach Claude your conventions, patterns, and standards
- **Automate workflows** — Create repeatable procedures like `/deploy` or `/review-pr`
- **Share across projects** — Define once, use everywhere

Skills follow the open [Agent Skills standard](https://agentskills.io).

---

## Quick Start

### 1. Create a Skill Directory

```bash
# Personal skill (all projects)
mkdir -p ~/.claude/skills/explain-code

# Project skill (this project only)
mkdir -p .claude/skills/explain-code
```

### 2. Write SKILL.md

Create `SKILL.md` inside your skill directory:

```yaml
---
name: explain-code
description: Explains code with diagrams and analogies. Use when asked "how does this work?"
---

When explaining code:

1. **Start with an analogy** — Compare to everyday concepts
2. **Draw a diagram** — Use ASCII art for flow/structure
3. **Walk through step-by-step** — Explain what happens
4. **Highlight gotchas** — Common mistakes to avoid
```

### 3. Invoke It

```
/explain-code src/auth/login.ts
```

Or just ask Claude to explain code — it will automatically detect the skill is relevant.

---

## Skill Locations

| Location | Path | Scope |
|----------|------|-------|
| Personal | `~/.claude/skills/<name>/SKILL.md` | All your projects |
| Project | `.claude/skills/<name>/SKILL.md` | Current project only |

Project skills override personal skills when names conflict.

---

## SKILL.md Format

```yaml
---
name: my-skill
description: What this skill does and when Claude should use it
---

# Instructions

Markdown content that Claude follows when the skill is active.
```

### Frontmatter Options

| Field | Required | Description |
|-------|----------|-------------|
| `name` | No | Display name (defaults to directory name) |
| `description` | Recommended | When to use this skill. Claude reads this to decide if it's relevant |
| `argument-hint` | No | Hint shown in autocomplete, e.g., `[filename]` |
| `disable-model-invocation` | No | Set `true` to prevent auto-invocation (manual `/skill` only) |
| `user-invocable` | No | Set `false` to hide from `/` menu (background knowledge) |
| `allowed-tools` | No | Restrict tools, e.g., `Read, Grep, Glob` |
| `model` | No | Override model, e.g., `opus` |
| `context` | No | Set `fork` to run in isolated subagent |
| `agent` | No | Subagent type when `context: fork` (`Explore`, `Plan`, etc.) |

---

## Variable Substitution

Use these in your skill content:

| Variable | Description |
|----------|-------------|
| `$ARGUMENTS` | All arguments passed to the skill |
| `$0`, `$1`, `$2`... | Individual arguments by position |
| `${CLAUDE_SESSION_ID}` | Current session ID |

**Example:**
```yaml
---
name: migrate-component
description: Migrate a component between frameworks
---

Migrate the `$0` component from $1 to $2.
Preserve all behavior and tests.
```

Usage: `/migrate-component SearchBar React Vue`

---

## Skill Types

### Reference Skills (Knowledge)

Teach Claude conventions — loads automatically when relevant:

```yaml
---
name: api-conventions
description: REST API design conventions for our services
---

# API Conventions

- Use kebab-case for URL paths
- Use camelCase for JSON properties
- Always paginate list endpoints
- Version APIs in URL path (/v1/, /v2/)
```

### Task Skills (Workflows)

Actionable procedures — typically invoked manually:

```yaml
---
name: fix-issue
description: Fix a GitHub issue
disable-model-invocation: true
---

Fix GitHub issue $ARGUMENTS:

1. Read the issue description
2. Understand requirements
3. Implement the fix
4. Write tests
5. Create a commit
```

Use `disable-model-invocation: true` for skills with side effects (deploying, committing, sending messages).

---

## Directory Structure

For complex skills, add supporting files:

```
my-skill/
├── SKILL.md           # Main instructions (required)
├── reference.md       # Detailed documentation
├── examples/
│   └── sample.md      # Example outputs
└── scripts/
    └── validate.sh    # Scripts Claude can run
```

Reference them from SKILL.md:

```markdown
For complete API details, see [reference.md](reference.md)
```

---

## Advanced Features

### Read-Only Mode

Restrict what tools Claude can use:

```yaml
---
name: safe-reader
description: Analyze code without making changes
allowed-tools: Read, Grep, Glob
---
```

### Forked Context

Run investigations in isolation:

```yaml
---
name: deep-research
description: Research a topic thoroughly
context: fork
agent: Explore
---

Research $ARGUMENTS and report findings with file references.
```

### Dynamic Context

Inject command output before Claude sees the skill:

```yaml
---
name: pr-summary
description: Summarize the current PR
---

## Current PR
- Diff: !`gh pr diff`
- Files: !`gh pr diff --name-only`

Summarize this pull request...
```

The `!`command`` syntax runs the command and injects output.

---

## Best Practices

| Practice | Why |
|----------|-----|
| Write specific descriptions | Claude accurately detects relevance |
| Keep SKILL.md under 500 lines | Reference long docs as separate files |
| Use `disable-model-invocation` for side effects | Prevents unintended actions |
| Include verification steps | "Run tests after implementation" |
| Provide examples | Claude understands expected output format |

---

## Examples

### Code Review Skill

```yaml
---
name: review
description: Review code for bugs, security issues, and best practices
---

Review the code and check for:

1. **Bugs** — Logic errors, edge cases, null checks
2. **Security** — Injection, auth issues, data exposure
3. **Performance** — N+1 queries, unnecessary loops
4. **Style** — Naming, formatting, comments

Provide specific line references and suggested fixes.
```

### Commit Skill

```yaml
---
name: commit
description: Create a well-formatted git commit
disable-model-invocation: true
---

Create a commit for the staged changes:

1. Run `git diff --staged` to see changes
2. Write a concise commit message (imperative mood)
3. Include a body if changes are complex
4. Run `git commit`
```

### Documentation Skill

```yaml
---
name: document
description: Generate documentation for code
argument-hint: [file-or-function]
---

Document $ARGUMENTS:

1. Read the code
2. Write a clear description
3. Document parameters and return values
4. Include usage examples
5. Note any gotchas or edge cases
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Skill not in `/` menu | Check file is named `SKILL.md` (case-sensitive) |
| Skill never triggers | Make description more specific with keywords |
| Skill triggers too often | Add `disable-model-invocation: true` |
| Arguments not working | Use `$ARGUMENTS` or `$0`, `$1` syntax |

---

## Quick Reference

```bash
# Create personal skill
mkdir -p ~/.claude/skills/my-skill
echo "---
name: my-skill
description: Does something useful
---

Instructions here..." > ~/.claude/skills/my-skill/SKILL.md

# Create project skill
mkdir -p .claude/skills/my-skill
# Same SKILL.md format

# Invoke
/my-skill
/my-skill with arguments
```

---

*For more details, see the [Claude Code documentation](https://docs.anthropic.com/en/docs/claude-code).*
