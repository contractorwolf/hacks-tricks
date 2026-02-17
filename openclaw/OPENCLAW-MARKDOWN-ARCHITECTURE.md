# OpenClaw Markdown Architecture Reference

This document provides a comprehensive guide to all markdown files that define an OpenClaw agent's personality, behavior, and capabilities. Understanding this architecture enables you to create, customize, and fine-tune AI characters for any purpose.

---

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [File Locations](#file-locations)
3. [Core Personality Files](#core-personality-files)
4. [Knowledge & Memory Files](#knowledge--memory-files)
5. [Skill Definition Files](#skill-definition-files)
6. [Configuration (JSON)](#configuration-json)
7. [Behavioral Techniques](#behavioral-techniques)
8. [Advanced Hacks & Patterns](#advanced-hacks--patterns)

---

## Architecture Overview

OpenClaw agents are defined through a layered system:

```
┌─────────────────────────────────────────────────────────────┐
│                     SYSTEM PROMPT                           │
│  (Built dynamically each session from files below)          │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
┌───────────────┐    ┌───────────────┐    ┌───────────────┐
│  PERSONALITY  │    │   KNOWLEDGE   │    │    SKILLS     │
│    FILES      │    │    FILES      │    │    FILES      │
├───────────────┤    ├───────────────┤    ├───────────────┤
│ SOUL.md       │    │ MEMORY.md     │    │ SKILL.md      │
│ AGENTS.md     │    │ memory/*.md   │    │ (per skill)   │
│ IDENTITY.md   │    │ knowledge/*   │    │               │
│ USER.md       │    │ TOOLS.md      │    │               │
└───────────────┘    └───────────────┘    └───────────────┘
                              │
                              ▼
                    ┌───────────────┐
                    │    CONFIG     │
                    │ openclaw.json │
                    ├───────────────┤
                    │ Model         │
                    │ Skills list   │
                    │ Tool allow/   │
                    │   deny lists  │
                    │ Routing       │
                    └───────────────┘
```

### How Files Become Behavior

1. **Session starts** → OpenClaw loads the agent config
2. **Workspace identified** → All `.md` files in workspace are candidates
3. **System prompt built** → Files are injected in priority order
4. **Skills loaded** → Each skill's `SKILL.md` is added when relevant
5. **Model receives** → Complete system prompt + conversation history

---

## File Locations

### Primary Locations

| Location | Purpose | Priority |
|----------|---------|----------|
| `~/.openclaw/workspaces/<name>/` | Per-agent workspace (personality, knowledge) | Highest |
| `~/.openclaw/skills/` | User-installed skills | High |
| `<workspace>/skills/` | Workspace-local skills | Highest (overrides) |
| `<install>/skills/` | Bundled skills | Lowest |
| `~/.openclaw/openclaw.json` | Global configuration | — |
| `~/.openclaw/agents/<id>/` | Agent runtime state (sessions, cache) | — |

### Workspace Directory Structure

```
~/.openclaw/workspaces/<agent-name>/
├── SOUL.md              # Core personality (REQUIRED)
├── AGENTS.md            # Operating procedures (REQUIRED)
├── IDENTITY.md          # Name, emoji, avatar
├── USER.md              # User context (who you're helping)
├── TOOLS.md             # Tool-specific notes, credentials, tips
├── BOOTSTRAP.md         # First-run ritual (deleted after use)
├── HEARTBEAT.md         # Periodic check tasks
├── MEMORY.md            # Long-term curated memory
├── memory/              # Daily logs
│   ├── 2024-01-15.md
│   ├── 2024-01-16.md
│   └── heartbeat-state.json
├── knowledge/           # Domain-specific content
│   ├── policies/
│   ├── procedures/
│   └── faq/
└── skills/              # Workspace-local skills (optional)
    └── custom-skill/
        └── SKILL.md
```

---

## Core Personality Files

### SOUL.md - Identity & Boundaries

**Location:** `<workspace>/SOUL.md`

**Purpose:** Defines WHO the agent is - personality, values, tone, and hard boundaries.

**When loaded:** Every session, first priority

**Affects:**
- How the agent speaks and responds
- What it will and won't do
- Its relationship with the user

#### Structure

```markdown
# SOUL.md - [Agent Name]

## Core Identity
[Who am I? What do I value? What's my purpose?]

## Tone & Voice
[How do I communicate? Formal/casual? Verbose/terse?]

## Boundaries
### I handle:
[Things I'm responsible for]

### I escalate:
[Things I pass to others]

### I never:
[Hard lines I won't cross]

## [Custom Sections]
[Protocol for specific situations]
```

#### Key Techniques

**Personality injection:**
```markdown
## Core Identity
You are warm but professional. You speak like a knowledgeable colleague,
not a formal assistant. You occasionally use humor but never at the
expense of helpfulness.
```

**Hard boundaries:**
```markdown
## I never:
- Share information about other users
- Make promises I can't keep
- Guess when I don't know - I say "I don't know" or look it up
```

**Tone calibration:**
```markdown
## Tone & Voice
- Use contractions: "I'll" not "I will"
- Avoid corporate speak: "Let me help" not "I would be happy to assist"
- Match user's energy: casual with casual, professional with professional
```

---

### AGENTS.md - Operating Procedures

**Location:** `<workspace>/AGENTS.md`

**Purpose:** Defines WHAT the agent does - procedures, workflows, knowledge sources.

**When loaded:** Every session, after SOUL.md

**Affects:**
- Session startup behavior
- How tasks are approached
- Knowledge source priorities
- Memory management

#### Structure

```markdown
# AGENTS.md - [Agent Role]

## Session Startup
[What to do at the start of every conversation]

## [Domain-Specific Procedures]
[Workflows, scripts, decision trees]

## Knowledge Sources
[What files to reference, when to search, etc.]

## Memory Management
[How to use daily notes, when to update MEMORY.md]

## Escalation
[When and how to hand off]

## Safety
[Domain-specific safety rules]
```

#### Key Techniques

**Session startup ritual:**
```markdown
## Session Startup
Before responding to any message:
1. Read SOUL.md (your personality)
2. Read knowledge/current-status.md (what's happening today)
3. Check if this is a follow-up conversation
4. Greet appropriately based on context
```

**Procedure scripts:**
```markdown
## Password Reset Procedure
1. Verify identity: Ask for employee ID and manager name
2. Confirm system: Which password needs reset?
3. Check status: Locked vs expired?
4. Execute reset: Use `user-tools` skill
5. Provide temp password: Remind them to change immediately
6. Confirm success: "Were you able to log in?"
```

**Knowledge routing:**
```markdown
## Knowledge Sources
- **Quick facts:** Check `knowledge/faq.md` first
- **Policies:** Reference `knowledge/policies/` for official rules
- **Dynamic info:** Use `search` skill for current information
- **Not found:** Say "Let me find out" and search or escalate
```

---

### IDENTITY.md - Metadata

**Location:** `<workspace>/IDENTITY.md`

**Purpose:** Basic identity metadata - name, emoji, avatar, vibe.

**When loaded:** Every session, used for display and self-reference

**Affects:**
- How the agent refers to itself
- Display name in interfaces
- Avatar/emoji in messages

#### Structure

```markdown
# IDENTITY.md

- **Name:** [Agent's name]
- **Creature:** [AI? Assistant? Something else?]
- **Vibe:** [One-line personality description]
- **Emoji:** [Signature emoji]
- **Avatar:** [Path or URL to avatar image]
```

#### Example

```markdown
# IDENTITY.md

- **Name:** Alex
- **Creature:** Your IT support buddy
- **Vibe:** Patient, clear, gets stuff done
- **Emoji:** 🖥️
- **Avatar:** avatars/alex-headset.png
```

---

### USER.md - User Context

**Location:** `<workspace>/USER.md`

**Purpose:** Information about the person(s) the agent is helping.

**When loaded:** Main sessions (not group chats with strangers)

**Affects:**
- How the agent addresses the user
- Personalization of responses
- Context for recommendations

#### Structure

```markdown
# USER.md - Who I'm Helping

## Name
[User's name and preferred address]

## Context
[Role, preferences, relevant background]

## Communication Preferences
[How they like to interact]

## Notes
[Anything else relevant]
```

#### Example

```markdown
# USER.md

## Name
James - prefers just "James", not "Mr. Wolf"

## Context
- Software developer, works from home
- Night owl - don't suggest morning appointments
- Has two kids - family scheduling matters

## Communication Preferences
- Likes concise answers, hates fluff
- Prefers bullet points over paragraphs
- OK with casual tone

## Notes
- Allergic to shellfish (relevant for restaurant recommendations)
- Uses 1Password for credentials
```

---

### TOOLS.md - Tool Notes

**Location:** `<workspace>/TOOLS.md`

**Purpose:** Local notes about tools, credentials, environment-specific configuration.

**When loaded:** Every session

**Affects:**
- How skills are used
- Environment-specific behavior
- Tool configuration

#### Example

```markdown
# TOOLS.md

## Cameras
- Front door: "Front Porch Camera"
- Backyard: "Deck Camera"
- Garage: "Garage Cam 1"

## Voice Preferences
- Default TTS voice: "Rachel" (ElevenLabs)
- Storytime voice: "Adam" (deeper, more dramatic)

## Local Services
- Home Assistant: http://homeassistant.local:8123
- Plex: http://plex.local:32400

## API Notes
- Google Places: Key in env, 1000 req/day limit
- Weather: Use OpenWeather, not Weather.gov (more reliable)
```

---

## Knowledge & Memory Files

### MEMORY.md - Long-term Memory

**Location:** `<workspace>/MEMORY.md`

**Purpose:** Curated, persistent memory across sessions. The agent's "long-term memory."

**When loaded:** Main sessions ONLY (not group chats)

**Security:** Contains personal context - never loaded in shared contexts

#### Best Practices

```markdown
# MEMORY.md

## Important Facts
- User's birthday: March 15
- Partner's name: Sarah
- Dog's name: Max (golden retriever)

## Preferences Learned
- Prefers dark mode everything
- Hates unnecessary notifications
- Likes productivity apps, skeptical of AI hype

## Ongoing Projects
- Home renovation: Kitchen remodel, contractor is Mike
- Work: Leading the API redesign project

## Lessons Learned
- 2024-01-10: User prefers I don't suggest alternatives unless asked
- 2024-01-15: Always confirm before sending emails on their behalf
```

---

### memory/*.md - Daily Notes

**Location:** `<workspace>/memory/YYYY-MM-DD.md`

**Purpose:** Raw logs of daily activity, conversations, events.

**When loaded:** Today + yesterday (configurable)

#### Example

```markdown
# 2024-01-16

## Conversations
- 9:15am: Helped with VPN setup, resolved by reinstalling client
- 2:30pm: Scheduled dentist appointment for Feb 3

## Tasks Completed
- Ordered replacement AirPods
- Updated home insurance policy

## Notes for Tomorrow
- Follow up on insurance confirmation email
- User mentioned wanting to plan a trip to Japan
```

---

### knowledge/* - Domain Content

**Location:** `<workspace>/knowledge/`

**Purpose:** Static reference content for the agent's domain.

**When loaded:** On demand (referenced in procedures)

#### Organization Patterns

**By type:**
```
knowledge/
├── policies/      # Official rules and guidelines
├── procedures/    # Step-by-step workflows
├── faq/           # Common questions and answers
└── reference/     # Lookup tables, contact lists
```

**By topic:**
```
knowledge/
├── products/      # Product catalog
├── shipping/      # Shipping info
├── returns/       # Return policies
└── stores/        # Store locations and hours
```

---

## Skill Definition Files

### SKILL.md - Skill Definition

**Location:** `<skill-dir>/SKILL.md`

**Purpose:** Defines how to use a skill - its capabilities, requirements, and procedures.

**When loaded:** When skill is needed (triggered by conversation context)

#### Structure

```yaml
---
name: skill-name
description: Brief description of what this skill does
homepage: https://example.com/tool
metadata:
  openclaw:
    emoji: "🔧"
    requires:
      bins: ["required-cli-tool"]
      env: ["REQUIRED_API_KEY"]
      config: ["config.path.required"]
    install:
      - id: "install-method"
        kind: "npm|go|brew|pip"
        package: "package-name"
        bins: ["installed-binary"]
        label: "Human-readable install option"
---

# Skill Name

[Skill description and when to use it]

## Setup

[Installation and configuration instructions]

## Usage

[How to use the skill - commands, API calls, etc.]

## Safety Rules

[What NOT to do with this skill]

## Examples

[Concrete examples of using the skill]
```

#### Example: Custom FAQ Skill

```yaml
---
name: company-faq
description: Answer common questions about Acme Corp products and policies
metadata:
  openclaw:
    emoji: "❓"
---

# Company FAQ

Answer common questions about Acme Corp without needing external lookups.

## Usage

When a user asks a common question, check this skill first before searching.

## Quick Answers

### Return Policy
"You can return any item within 30 days for a full refund. Items must be unused and in original packaging. Electronics have a 15-day return window."

### Shipping Times
"Standard shipping is 5-7 business days. Express is 2-3 days. Next-day delivery is available for orders placed by 2pm."

### Contact
"Customer service: 1-800-ACME-HELP, Mon-Fri 9am-6pm EST. Email: support@acmecorp.com"

### Price Match
"We match prices from major retailers for identical in-stock items. Bring proof of the competitor's price within 14 days of purchase."
```

---

## Configuration (JSON)

### openclaw.json - Agent Configuration

**Location:** `~/.openclaw/openclaw.json`

**Purpose:** Defines agent list, routing, model selection, and tool restrictions.

#### Agent Definition

```json
{
  "agents": {
    "defaults": {
      "workspace": "~/.openclaw/workspace",
      "model": {
        "primary": "anthropic/claude-sonnet-4-5",
        "fallbacks": ["anthropic/claude-haiku-4-5"]
      },
      "thinkingDefault": "low"
    },
    "list": [
      {
        "id": "main",
        "name": "Main Assistant",
        "default": true,
        "workspace": "~/.openclaw/workspace"
      },
      {
        "id": "support",
        "name": "Support Agent",
        "workspace": "~/.openclaw/workspaces/support",
        "model": "anthropic/claude-haiku-4-5",
        "skills": ["ticket-system", "user-lookup"],
        "tools": {
          "allow": ["read", "search"],
          "deny": ["write", "edit", "exec"]
        }
      }
    ]
  }
}
```

#### Key Configuration Options

| Field | Purpose |
|-------|---------|
| `id` | Unique identifier for the agent |
| `name` | Display name |
| `workspace` | Path to workspace directory |
| `model` | Model or {primary, fallbacks} |
| `skills` | Allowlist of skill IDs (if set, only these skills available) |
| `tools.allow` | Whitelist of allowed tools |
| `tools.deny` | Blacklist of denied tools |
| `identity` | Override name/emoji from workspace |
| `groupChat` | Group chat behavior settings |
| `sandbox` | Docker sandboxing options |

#### Routing Configuration

```json
{
  "bindings": [
    {
      "agentId": "support",
      "match": { "channel": "telegram" }
    },
    {
      "agentId": "family",
      "match": {
        "channel": "whatsapp",
        "peer": { "kind": "group", "id": "120363...@g.us" }
      }
    }
  ]
}
```

---

## Behavioral Techniques

### 1. Constraining Scope

**Technique:** Explicit boundary lists in SOUL.md

```markdown
## I handle:
- Questions about our products
- Order status inquiries
- Return requests

## I don't handle:
- Technical support for third-party products
- Billing disputes over $100
- Anything requiring account access

## If asked about something I don't handle:
> "That's outside what I can help with directly. Let me connect you with [specific team/person] who can assist."
```

### 2. Tone Calibration

**Technique:** Explicit voice guidelines with examples

```markdown
## Voice Examples

### Too formal:
"I would be delighted to assist you with your inquiry regarding the status of your order."

### Too casual:
"yo let me check on that order real quick lol"

### Just right:
"Let me look that up for you. One moment."
```

### 3. Response Templates

**Technique:** Pre-written responses for common situations

```markdown
## Standard Responses

### Greeting (new conversation):
"Hi! How can I help you today?"

### Greeting (returning user):
"Welcome back! What can I do for you?"

### Closing:
"Is there anything else I can help with?"

### Escalation:
"I want to make sure you get the best help on this. Let me connect you with [X] who specializes in this."
```

### 4. Decision Trees

**Technique:** Explicit flowcharts in AGENTS.md

```markdown
## Return Request Flow

```
Customer requests return
         │
         ▼
   Within 30 days?
    ┌────┴────┐
   Yes        No
    │          │
    ▼          ▼
 Full       Store credit
 refund     or escalate
    │          │
    ▼          ▼
 Process    Check if
 return     exception
            applies
```
```

### 5. Fallback Chains

**Technique:** Layered knowledge sources

```markdown
## Finding Information

1. **Check workspace knowledge first:** `knowledge/` directory
2. **If not found, search:** Use web search skill
3. **If uncertain, verify:** Cross-reference multiple sources
4. **If still unsure:** Say so and offer to escalate

Never guess. Never make up information.
```

---

## Advanced Hacks & Patterns

### 1. Persona Switching

**Use case:** Different personalities for different contexts

**Technique:** Multiple SOUL sections with triggers

```markdown
# SOUL.md

## Default Persona
Professional, helpful, efficient.

## Casual Mode (trigger: user says "chill" or uses lots of emoji)
More relaxed, uses casual language, occasional humor.

## Serious Mode (trigger: topic is medical, legal, financial)
Extra careful, always recommends professional advice, no humor.
```

### 2. Skill Gating

**Use case:** Prevent dangerous operations

**Technique:** Combine config restrictions with skill warnings

```json
// In openclaw.json
{
  "tools": {
    "deny": ["exec", "write", "edit"]
  }
}
```

```markdown
// In SKILL.md
## Safety

NEVER:
- Execute commands without explicit user confirmation
- Modify files without showing the change first
- Run anything that could affect system state
```

### 3. Memory Priming

**Use case:** Agent "remembers" key context

**Technique:** Structured MEMORY.md sections

```markdown
# MEMORY.md

## Critical Context (always relevant)
- User is CEO of StartupCo
- Extremely busy, values brevity
- Has assistant named Maria for scheduling

## Current Projects (reference as needed)
- Series B fundraise (closing next month)
- Hiring VP of Engineering
- Board meeting Feb 15

## Past Lessons (inform behavior)
- 2024-01: User prefers Slack over email
- 2024-01: Never schedule calls before 10am
```

### 4. Escalation Triggers

**Use case:** Automatic handoff for specific situations

**Technique:** Explicit trigger patterns in AGENTS.md

```markdown
## Immediate Escalation Triggers

If any of these occur, stop and escalate immediately:

1. **Legal threat:** User mentions "lawyer", "lawsuit", "legal action"
2. **Safety concern:** User mentions self-harm, violence, abuse
3. **Fraud indicator:** Multiple failed verifications, inconsistent info
4. **Anger escalation:** User curses at you, makes threats
5. **Policy exception:** Request requires breaking stated rules

Escalation response:
> "I want to make sure you get the right help. I'm connecting you with a supervisor now."
```

### 5. Context Windowing

**Use case:** Control what gets loaded when

**Technique:** Conditional loading in AGENTS.md

```markdown
## Context Loading

### Always load:
- SOUL.md
- IDENTITY.md

### Load in main session only:
- MEMORY.md
- USER.md

### Load on demand:
- knowledge/* (when topic comes up)
- skills/* (when capability needed)

### Never load in group chats:
- MEMORY.md (contains private info)
- USER.md (personal context)
```

### 6. Tool Usage Patterns

**Use case:** Guide when/how to use tools

**Technique:** Tool decision matrix in AGENTS.md

```markdown
## Tool Selection

| Need | Tool | Notes |
|------|------|-------|
| Quick fact | Check knowledge/ first | Don't search if you know |
| Current info | web_search | Weather, news, prices |
| User data | user-lookup skill | Verify identity first |
| Create ticket | ticket-system skill | Always after troubleshooting |

### Tool Order
1. Memory/knowledge (free, fast)
2. Skills (has constraints)
3. Search (slower, costs tokens)
4. Ask user (last resort)
```

### 7. Output Formatting

**Use case:** Consistent response structure

**Technique:** Format templates in AGENTS.md

```markdown
## Response Formats

### For status inquiries:
```
**Order #[number]**
Status: [status]
Location: [location]
ETA: [date]
```

### For recommendations:
```
Here are [N] options:

1. **[Name]** - [Brief description]
   [Key detail]

2. **[Name]** - [Brief description]
   [Key detail]

Would you like more details on any of these?
```

### For errors:
```
I wasn't able to [action] because [reason].

What I can do instead:
- [Alternative 1]
- [Alternative 2]

Would either of those work?
```
```

### 8. Proactive Behavior

**Use case:** Agent initiates when appropriate

**Technique:** HEARTBEAT.md checklist

```markdown
# HEARTBEAT.md

## Check every heartbeat (rotate through):
- [ ] Unread emails (urgent only)
- [ ] Calendar: anything in next 2 hours?
- [ ] Weather: if user mentioned going out

## Proactive triggers:
- Important email arrived → notify immediately
- Meeting in 30 min → reminder
- Weather alert → mention if user might be affected

## Stay quiet when:
- After 11pm / before 8am (unless urgent)
- Nothing new since last check
- User marked as busy
```

---

## Quick Reference: File → Behavior

| File | Behavior Controlled |
|------|---------------------|
| SOUL.md | Personality, tone, boundaries |
| AGENTS.md | Procedures, workflows, escalation |
| IDENTITY.md | Name, emoji, self-reference |
| USER.md | Personalization, user context |
| TOOLS.md | Tool-specific configuration |
| MEMORY.md | Long-term memory, lessons learned |
| memory/*.md | Recent context, daily events |
| knowledge/* | Domain facts, policies, FAQs |
| SKILL.md | Tool capabilities and usage |
| openclaw.json | Model, skills, tool restrictions |

---

## Debugging Checklist

When agent behavior is wrong:

1. **Wrong personality?** → Check SOUL.md
2. **Wrong procedure?** → Check AGENTS.md
3. **Missing context?** → Check if MEMORY.md/USER.md loaded
4. **Wrong tools?** → Check skills list in config
5. **Doing forbidden things?** → Check tools.deny in config
6. **Too verbose/terse?** → Add tone examples to SOUL.md
7. **Not following script?** → Make procedure more explicit in AGENTS.md
