# Context Forking with Claude Code

A workflow pattern for managing long coding sessions using `/resume` to fork from a perfected context.

---

## The Problem

Claude Code has a token limit. As you work, context accumulates:
- Files read
- Edits made
- Conversation history
- Tool outputs

Eventually you hit the limit and context gets compacted or you lose valuable setup work.

---

## The Solution: Context Forking

**Build once, fork many.** Create a "perfected context" session, then spawn fresh sessions from it.

```
┌─────────────────────────────────────────────────────────┐
│  SEED SESSION (keep pristine)                           │
│  - Read key files                                       │
│  - Understand architecture                              │
│  - Review plans/issues                                  │
│  - NO implementation work                               │
└─────────────────────────────────────────────────────────┘
            │
            ├──────────────────┬──────────────────┐
            ▼                  ▼                  ▼
   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
   │  Fork #1    │    │  Fork #2    │    │  Fork #3    │
   │  Fix Issue  │    │  Add Feature│    │  Refactor   │
   │  (fresh     │    │  (fresh     │    │  (fresh     │
   │   tokens)   │    │   tokens)   │    │   tokens)   │
   └─────────────┘    └─────────────┘    └─────────────┘
```

---

## Step-by-Step Workflow

### 1. Create the Seed Session

Start a new Claude Code session focused purely on context building:

```bash
claude
```

Then build understanding without doing implementation work:

```
> Read the implementation plan and code review, let me know when ready to discuss

> Explain the architecture of the network module

> What are the dependencies between these issues?
```

**Key principle:** Don't make changes. Just read, analyze, and confirm understanding.

### 2. Verify the Context

Before forking, ensure Claude has internalized:

- [ ] Project structure and conventions
- [ ] The specific task/plan to implement
- [ ] Key files and their relationships
- [ ] Constraints or gotchas

Ask clarifying questions:
```
> Summarize the 3 highest priority fixes

> Which files will you need to modify?

> What's the verification step for each fix?
```

### 3. Save the Session ID

Note your session ID (shown in the prompt or via `/sessions`):

```
Session: abc123-def456-...
```

This is your "golden" context checkpoint.

### 4. Fork for Implementation

Start a new session resuming from your seed:

```bash
claude --resume abc123-def456
```

Or within Claude Code:
```
/resume abc123-def456
```

Now you have:
- Full context from the seed session
- Fresh token budget for implementation
- Clean conversation history

### 5. Do the Work

In the forked session, implement one focused task:

```
> Implement Phase 1 from the plan - move the API key to secrets.h
```

Complete the task, commit if needed, then exit.

### 6. Fork Again for Next Task

Return to your seed session ID and fork again:

```bash
claude --resume abc123-def456
```

```
> Implement Phase 2 - add null checks to callbacks
```

Each fork starts fresh but retains the foundational understanding.

---

## Best Practices

### DO

- **Keep seed sessions read-only** - No edits, just comprehension
- **One task per fork** - Stay focused, avoid context bloat
- **Name your tasks clearly** - "Fix null checks in Network.cpp"
- **Commit between forks** - Clean state for next session

### DON'T

- **Don't implement in the seed** - You'll burn tokens and can't reuse it
- **Don't fork mid-task** - Finish or abandon, then fork fresh
- **Don't nest forks** - Always fork from the original seed

---

## Example: PocketGPT Stabilization

### Seed Session
```
> Read IMPLEMENTATION_PLAN.md and CODE_REVIEW.md
> Read the 5 source files that need changes
> Confirm understanding of the 9 fixes needed
```

### Fork 1: Security Fix
```bash
claude --resume <seed-id>
```
```
> Create secrets.h, update .gitignore, modify PocketGPT_Config.hpp
```

### Fork 2: Null Checks
```bash
claude --resume <seed-id>
```
```
> Add null checks to all callbacks in PocketGPT_Network.cpp
```

### Fork 3: Task Memory Leaks
```bash
claude --resume <seed-id>
```
```
> Check xTaskCreate results in PocketGPT_SDCard.cpp
```

Each fork completes one phase with full context and maximum available tokens.

---

## When to Use This Pattern

**Good fit:**
- Multi-phase implementation plans
- Large refactoring efforts
- Bug fix batches
- Code review remediation

**Overkill for:**
- Single quick fixes
- One-off questions
- Small features

---

## Troubleshooting

### "Session not found"
Sessions expire. Recreate your seed session if needed.

### "Context seems stale"
If files changed significantly since the seed, create a new seed session.

### "Fork diverged too much"
If a fork goes off-track, abandon it and fork fresh from seed.

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `/sessions` | List recent sessions |
| `/resume <id>` | Fork from a session |
| `claude --resume <id>` | Fork from CLI |
| `/context` | Check current token usage |

---

*This pattern maximizes the value of context-building work while keeping implementation sessions lean and focused.*
