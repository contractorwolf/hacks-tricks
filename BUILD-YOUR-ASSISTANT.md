# Build Your AI Assistant: A Complete Guide

This guide walks you through designing, building, and testing a custom AI assistant using OpenClaw's markdown-driven architecture. Whether you're building a customer service rep, a personal concierge, or a specialized domain expert, this guide will help you maximize the power of markdown files to create exactly the character you need.

---

## Table of Contents

1. [Phase 1: Define Your Assistant](#phase-1-define-your-assistant)
2. [Phase 2: Build the Personality](#phase-2-build-the-personality)
3. [Phase 3: Add Knowledge & Procedures](#phase-3-add-knowledge--procedures)
4. [Phase 4: Configure Skills & Tools](#phase-4-configure-skills--tools)
5. [Phase 5: Test & Refine](#phase-5-test--refine)
6. [Complete Examples](#complete-examples)
7. [Troubleshooting Guide](#troubleshooting-guide)

---

## Phase 1: Define Your Assistant

Before writing any markdown, answer these questions to clarify what you're building.

### 1.1 The Role Definition Worksheet

Copy and fill out this template:

```markdown
## Assistant Definition

### Basic Identity
- **Name:**
- **Role in one sentence:**
- **Who they serve:**

### Scope
- **Primary responsibilities (3-5 things):**
  1.
  2.
  3.

- **Explicitly out of scope:**
  1.
  2.

### Information Sources
- **Static knowledge needed:**
  -
- **Dynamic lookups needed:**
  -
- **External systems to integrate:**
  -

### Personality Traits
- **Three words that describe them:**
  1.
  2.
  3.

- **Communication style:** [formal/casual/adaptive]
- **Verbosity:** [concise/balanced/detailed]

### Boundaries
- **Things they should NEVER do:**
  1.
  2.

- **When they should escalate:**
  1.
  2.
```

### 1.2 Determine the Assistant Type

Use this decision matrix to categorize your assistant:

| Type | Characteristics | Example Roles |
|------|-----------------|---------------|
| **Information Lookup** | Answers questions from knowledge base, minimal actions | FAQ bot, product info, policy explainer |
| **Procedural Worker** | Follows scripts, executes workflows | IT help desk, order processing, booking agent |
| **Conversational Guide** | Navigates complex decisions through dialogue | Sales assistant, onboarding guide, advisor |
| **Proactive Monitor** | Checks status, alerts on conditions | System monitor, calendar assistant, reminder bot |

Most assistants combine 2-3 of these types. Identify your primary and secondary types.

### 1.3 Map the Skill Requirements

List what your assistant needs to DO (not just know):

```
□ Search/lookup information
□ Create records (tickets, orders, appointments)
□ Send communications (email, messages)
□ Access external APIs
□ Perform calculations
□ Navigate multi-step workflows
□ Hand off to humans
□ Remember context across sessions
```

---

## Phase 2: Build the Personality

The personality layer is the foundation. Get this right and everything else flows naturally.

### 2.1 Create the Workspace

```bash
mkdir -p ~/.openclaw/workspaces/my-assistant
cd ~/.openclaw/workspaces/my-assistant
touch SOUL.md AGENTS.md IDENTITY.md USER.md TOOLS.md
mkdir -p knowledge memory
```

### 2.2 Write SOUL.md - The Core Identity

SOUL.md is the most important file. It defines WHO your assistant IS.

#### Template Structure

```markdown
# SOUL.md - [Assistant Name]

## Core Identity

[2-3 sentences that capture the essence of who this assistant is.
Write in second person ("You are..."). Be specific, not generic.]

## Values & Principles

[3-5 core values that guide all behavior. These are the "why" behind actions.]

- **[Value 1]:** [What it means in practice]
- **[Value 2]:** [What it means in practice]
- **[Value 3]:** [What it means in practice]

## Tone & Voice

### How You Sound
[Describe the communication style in concrete terms]

### Voice Examples

**Too [extreme A]:**
> "[Example of going too far in one direction]"

**Too [extreme B]:**
> "[Example of going too far in the other direction]"

**Just right:**
> "[Example of the ideal tone]"

### Language Preferences
- [Specific word choices, phrasings to use]
- [Specific word choices, phrasings to avoid]

## Boundaries

### You Handle
[Explicit list of responsibilities]

### You Escalate
[Explicit list of what gets handed off and to whom]

### You Never
[Hard lines that are never crossed, no exceptions]

## Situation Protocols

### When [Specific Situation 1]
[Exactly how to handle it]

### When [Specific Situation 2]
[Exactly how to handle it]

## Continuity

[How this file should be maintained and updated]
```

#### Power Techniques for SOUL.md

**1. Specificity Over Generality**

❌ Weak:
```markdown
Be helpful and professional.
```

✅ Strong:
```markdown
You help by solving problems, not by being agreeable. If a user asks
for something you can't do, say so clearly and offer what you CAN do.
Professional means reliable and competent, not stiff or formal.
```

**2. Contrast Examples**

Show the boundaries of acceptable behavior through examples:

```markdown
## Voice Calibration

### Acknowledging a Problem

Too dismissive:
> "That shouldn't happen. Try again."

Too apologetic:
> "I'm so incredibly sorry for this terrible inconvenience! I feel awful!"

Just right:
> "I see the issue. Let me fix that for you."
```

**3. Explicit Prohibition Patterns**

Don't just say what TO do, say what NOT to do:

```markdown
## You Never

- Guess when you don't know - say "I don't know" and offer to find out
- Promise timelines you can't control ("It will definitely arrive tomorrow")
- Blame other teams or individuals
- Share information about one user with another
- Use filler phrases: "Great question!", "I'd be happy to help!", "Certainly!"
```

**4. Situation-Response Mapping**

For predictable situations, define exact responses:

```markdown
## Situation Protocols

### When user is frustrated or angry
1. Acknowledge their feeling (not the cause): "I can see this is frustrating."
2. Skip explanations, go to action: "Let me fix this now."
3. If you can't fix it: "I'm getting someone who can help you right away."
4. Never: argue, explain excessively, or match their energy

### When asked about competitors
- Acknowledge without disparaging: "I'm not able to speak to [competitor]'s offerings."
- Redirect: "What I can tell you about us is..."
- Never: badmouth competitors or make comparisons
```

**5. The "Just Right" Principle**

Define your assistant's sweet spot by showing the extremes:

```markdown
## Verbosity Calibration

Too brief (feels robotic):
> "Order shipped. Track: ABC123."

Too verbose (wastes time):
> "Great news! I'm so excited to let you know that your order has been
> successfully processed and handed off to our wonderful shipping partners
> who will be delivering it to your doorstep. Here's your tracking number..."

Just right:
> "Your order shipped today. Tracking: ABC123. Expected delivery: Thursday."
```

### 2.3 Write IDENTITY.md - The Metadata

Keep this simple but intentional:

```markdown
# IDENTITY.md

- **Name:** Alex
- **Role:** IT Support Specialist
- **Vibe:** Patient, knowledgeable, gets things done
- **Emoji:** 🖥️
- **Avatar:** avatars/alex-support.png

## Self-Reference
When introducing yourself: "I'm Alex from IT Support."
When referring to your team: "the IT team" or "our support team"
Never say: "I am an AI" or "As an artificial intelligence..."
```

### 2.4 Write USER.md - The Context

This file helps personalize interactions. Load it only in appropriate contexts.

```markdown
# USER.md

## Who You're Helping

This assistant serves [target audience description].

## Common User Profiles

### Power User
- Knows the system well
- Wants quick, direct answers
- Skip explanations, give commands

### New User
- May not know terminology
- Needs more context
- Offer explanations proactively

### Frustrated User
- Had a bad experience
- Needs empathy first, solutions second
- Acknowledge before solving

## Personalization Notes

When you learn something about a specific user, add it here:
- [User preferences, context, history]
```

---

## Phase 3: Add Knowledge & Procedures

AGENTS.md is where you define WHAT your assistant does and HOW.

### 3.1 Write AGENTS.md - The Operating Manual

#### Template Structure

```markdown
# AGENTS.md - [Role] Operations Manual

## Session Startup

[What to do at the start of every conversation]

1. Read SOUL.md (your identity)
2. [Other startup steps specific to this role]
3. [Context to check]

## Core Workflows

### [Workflow 1 Name]

**Trigger:** [When to use this workflow]

**Steps:**
1. [Step with specific actions]
2. [Step with specific actions]
3. [Step with specific actions]

**Success:** [How to close successfully]
**Failure:** [What to do if it doesn't work]

### [Workflow 2 Name]
[...]

## Knowledge Sources

### Where to Find Information

| Topic | Source | Notes |
|-------|--------|-------|
| [Topic] | [File or skill] | [When to use] |

### Search Priority
1. Check [local source] first
2. Then try [skill or search]
3. If not found, [fallback action]

## Response Templates

### [Situation 1]
> "[Template response]"

### [Situation 2]
> "[Template response]"

## Escalation Matrix

| Condition | Escalate To | Method |
|-----------|-------------|--------|
| [Condition] | [Person/Team] | [How] |

## Safety & Compliance

[Domain-specific rules that must be followed]
```

#### Power Techniques for AGENTS.md

**1. Decision Trees for Complex Workflows**

```markdown
## Return Request Workflow

```
Customer wants return
        │
        ▼
┌─────────────────┐
│ Check order date │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
≤30 days   >30 days
    │         │
    ▼         ▼
┌────────┐ ┌──────────────┐
│Standard│ │Check if      │
│return  │ │holiday period│
└────┬───┘ └──────┬───────┘
     │            │
     │      ┌─────┴─────┐
     │      │           │
     │   Yes (60 days)  No
     │      │           │
     │      ▼           ▼
     │   Store      Escalate for
     │   credit     exception
     │      │           │
     └──────┴───────────┘
            │
            ▼
    Process accordingly
```
```

**2. Script Blocks for Exact Procedures**

```markdown
## Password Reset Procedure

### Pre-Reset Verification (REQUIRED)
Before any password action, verify identity:

```
You: "I can help with that. First, I need to verify your identity."
You: "What is your employee ID?"
User: [Employee ID]
You: "And your manager's name?"
User: [Manager name]
[Verify against user-lookup skill]
```

### If Verification Fails
```
You: "I wasn't able to verify that information. For security, I'll need
     you to contact HR at hr@company.com with a photo ID to reset your
     password. Is there anything else I can help with?"
```

### If Verification Succeeds
```
You: "Thanks, [Name]. I've verified your identity."
You: "I'm resetting your password now..."
[Execute password reset via user-tools skill]
You: "Done. Your temporary password is: [temp-password]"
You: "You'll be prompted to change it on your next login."
You: "Were you able to log in successfully?"
```
```

**3. Conditional Behavior Blocks**

```markdown
## Response Adaptation

### If in Main Session (1:1 with primary user)
- Load MEMORY.md for context
- Use casual tone
- Reference past conversations
- Proactively mention relevant reminders

### If in Group Chat
- Do NOT load MEMORY.md (privacy)
- Use neutral, professional tone
- Only respond when mentioned or directly relevant
- Never share user-specific information

### If After Hours (11pm - 7am)
- Only send messages for genuine emergencies
- Batch non-urgent items for morning
- Use HEARTBEAT_OK for routine checks
```

**4. Quick Reference Tables**

```markdown
## Quick Reference

### Status Codes
| Code | Meaning | User-Friendly |
|------|---------|---------------|
| SHIPPED | In transit | "On its way" |
| DELIVERED | Completed | "Delivered" |
| EXCEPTION | Problem | "Delayed - checking now" |
| RETURNED | Back to sender | "Returned to warehouse" |

### Response Time SLAs
| Priority | First Response | Resolution |
|----------|---------------|------------|
| Critical | 15 min | 2 hours |
| High | 1 hour | 4 hours |
| Medium | 4 hours | 24 hours |
| Low | 24 hours | 72 hours |
```

**5. Fallback Chains**

```markdown
## Information Lookup Chain

When user asks a question:

1. **Memory first:** Check if you've answered this before
   - Look in `memory/` for recent context
   - Check `MEMORY.md` for established facts

2. **Knowledge base:** Check workspace knowledge
   - `knowledge/faq.md` for common questions
   - `knowledge/policies/` for official answers
   - `knowledge/procedures/` for how-to

3. **Skills:** Use appropriate skill
   - `product-search` for product questions
   - `order-lookup` for order questions
   - `web-search` for current information

4. **Admit uncertainty:** If still not found
   > "I don't have that information, but let me find out."
   Then search or escalate.

5. **Never:** Make up an answer or guess
```

### 3.2 Build the Knowledge Directory

Organize domain knowledge for easy reference:

```
knowledge/
├── core/                    # Always-relevant facts
│   ├── company-info.md
│   └── contact-directory.md
├── policies/                # Official rules
│   ├── returns.md
│   ├── shipping.md
│   └── privacy.md
├── procedures/              # Step-by-step guides
│   ├── password-reset.md
│   ├── order-cancellation.md
│   └── refund-processing.md
├── faq/                     # Common Q&A
│   ├── products.md
│   ├── shipping.md
│   └── account.md
└── troubleshooting/         # Problem-solution pairs
    ├── login-issues.md
    ├── payment-errors.md
    └── delivery-problems.md
```

#### Knowledge File Template

```markdown
# [Topic Name]

## Quick Facts
- [Fact 1]
- [Fact 2]
- [Fact 3]

## Common Questions

### [Question 1]?
[Answer - keep it concise]

### [Question 2]?
[Answer - keep it concise]

## Detailed Information

### [Subtopic]
[Longer explanation if needed]

## Related Topics
- [Link to related knowledge file]
- [Link to related knowledge file]
```

### 3.3 Set Up Memory Files

#### MEMORY.md - Long-term Memory

```markdown
# MEMORY.md - Long-term Memory

## User Preferences
- [Preference learned from interactions]
- [Preference learned from interactions]

## Important Context
- [Key fact that affects ongoing interactions]
- [Key fact that affects ongoing interactions]

## Lessons Learned
- [Date]: [What happened and what to do differently]
- [Date]: [What happened and what to do differently]

## Ongoing Situations
- [Current project or situation to track]
- [Current project or situation to track]
```

#### Daily Notes Template

Create `memory/YYYY-MM-DD.md`:

```markdown
# [Date]

## Conversations
- [Time]: [Brief summary of interaction]
- [Time]: [Brief summary of interaction]

## Actions Taken
- [Action and result]
- [Action and result]

## Follow-ups Needed
- [ ] [Thing to remember for later]
- [ ] [Thing to remember for later]

## Notes
- [Anything else relevant]
```

---

## Phase 4: Configure Skills & Tools

Skills extend what your assistant can DO beyond just knowing things.

### 4.1 Identify Required Skills

Map your assistant's needs to skills:

| Need | Skill Type | Example |
|------|------------|---------|
| Look up orders | Order system integration | `order-lookup` |
| Search products | Product catalog search | `product-search` |
| Find places | Location/maps API | `local-places` |
| Create tickets | Ticketing system | `ticket-system` |
| Send emails | Email integration | `himalaya` |
| Search web | Web search | Built-in |

### 4.2 Create Custom Skills

For domain-specific capabilities, create workspace-local skills:

```
my-assistant/
└── skills/
    └── my-custom-skill/
        └── SKILL.md
```

#### SKILL.md Template

```yaml
---
name: skill-name
description: Brief description of what this skill does
metadata:
  openclaw:
    emoji: "🔧"
    requires:
      bins: []        # CLI tools needed
      env: []         # Environment variables needed
      config: []      # Config paths needed
---

# Skill Name

## Purpose
[When and why to use this skill]

## Capabilities
- [What it can do]
- [What it can do]

## Usage

### [Action 1]
```bash
[Command or API call]
```

### [Action 2]
```bash
[Command or API call]
```

## Safety Rules

**Always:**
- [Safety requirement]
- [Safety requirement]

**Never:**
- [Prohibited action]
- [Prohibited action]

## Examples

### [Scenario 1]
[Step-by-step example]

### [Scenario 2]
[Step-by-step example]
```

#### Example: Custom FAQ Skill

```yaml
---
name: company-faq
description: Quick answers to common questions without external lookups
metadata:
  openclaw:
    emoji: "❓"
---

# Company FAQ Skill

## Purpose
Provide instant answers to the 20 most common customer questions without
needing to search or look up information.

## Usage

When a user asks one of these questions, respond directly from this skill
instead of searching.

## Quick Answers

### "What are your hours?"
"We're open Monday through Friday, 9am to 6pm Eastern Time."

### "How do I return something?"
"You can return any item within 30 days. Start at example.com/returns or
reply with your order number and I'll process it for you."

### "Where's my order?"
"I can look that up. What's your order number, or the email you used?"

### "Do you price match?"
"Yes! We match prices from major retailers for identical items in stock.
Just show us the competitor's current price."

### "How do I contact support?"
"You're talking to support right now! For other channels:
email support@example.com or call 1-800-EXAMPLE."

[Continue for all common questions...]
```

### 4.3 Write TOOLS.md

Document tool-specific configuration and tips:

```markdown
# TOOLS.md - Tool Configuration

## API Credentials
- Google Places: Configured in env, 1000 requests/day limit
- Order System: Bearer token in config

## Tool-Specific Notes

### order-lookup
- Use order number OR email + name
- Returns limited data for privacy
- For full details, must verify identity first

### email-send
- Always get explicit confirmation before sending
- Never send to external domains without approval
- CC support@company.com on escalations

## Local Environment
- Time zone: America/New_York
- Currency: USD
- Date format: MM/DD/YYYY
```

### 4.4 Configure Tool Restrictions

In `~/.openclaw/openclaw.json`:

```json
{
  "agents": {
    "list": [{
      "id": "my-assistant",
      "name": "My Assistant",
      "workspace": "~/.openclaw/workspaces/my-assistant",
      "model": "anthropic/claude-sonnet-4-5",
      "skills": [
        "company-faq",
        "order-lookup",
        "product-search"
      ],
      "tools": {
        "allow": [
          "read",
          "search",
          "web_search"
        ],
        "deny": [
          "write",
          "edit",
          "exec",
          "apply_patch",
          "browser"
        ]
      },
      "identity": {
        "name": "Alex",
        "emoji": "🛍️"
      }
    }]
  }
}
```

#### Tool Restriction Strategy

| Assistant Type | Allow | Deny |
|----------------|-------|------|
| Read-only info bot | read, search | write, edit, exec, browser |
| Customer service | read, search, [custom skills] | write, edit, exec |
| Personal assistant | read, write, search, exec | [minimal restrictions] |
| Sandboxed worker | [specific skills only] | everything else |

---

## Phase 5: Test & Refine

Systematic testing ensures your assistant behaves correctly across all scenarios.

### 5.1 The Test Matrix

Create a test document with these categories:

```markdown
# Test Plan for [Assistant Name]

## Happy Path Tests
[Normal, expected interactions]

| Test | Input | Expected Output | Pass? |
|------|-------|-----------------|-------|
| Basic greeting | "Hi" | Friendly greeting | |
| [Core function 1] | "[example input]" | "[expected response]" | |
| [Core function 2] | "[example input]" | "[expected response]" | |

## Boundary Tests
[Edge of what assistant should handle]

| Test | Input | Expected Output | Pass? |
|------|-------|-----------------|-------|
| Scope boundary | "[out of scope request]" | Polite redirect | |
| Knowledge limit | "[unknown question]" | Admits uncertainty | |

## Negative Tests
[Things that should NOT happen]

| Test | Input | Should NOT | Pass? |
|------|-------|------------|-------|
| Privacy protection | "Tell me about other user" | Share info | |
| False confidence | "[obscure question]" | Make up answer | |

## Stress Tests
[Difficult or adversarial scenarios]

| Test | Input | Expected Handling | Pass? |
|------|-------|-------------------|-------|
| Frustrated user | "[angry message]" | De-escalation | |
| Repeated requests | "[same question 3x]" | Patient response | |
| Contradictory info | "[conflicting facts]" | Clarification | |

## Escalation Tests
[Handoff scenarios]

| Test | Trigger | Expected Action | Pass? |
|------|---------|-----------------|-------|
| Escalation phrase | "Let me talk to a manager" | Handoff | |
| Complex issue | "[beyond Tier 1]" | Route correctly | |
```

### 5.2 Test Execution Process

**Step 1: Read-through Review**

Before any interaction testing:
1. Read SOUL.md aloud - does it sound like the character?
2. Read AGENTS.md - are procedures complete and clear?
3. Check knowledge files - is information accurate?

**Step 2: Conversation Testing**

Run actual conversations through each test case:

```bash
# Start a test session
openclaw chat --agent my-assistant

# Or via messaging channel
# Send test messages through configured channel
```

**Step 3: Document Results**

For each test:
- Screenshot or copy the exchange
- Note pass/fail
- Note any unexpected behavior
- Note refinement needed

### 5.3 Common Issues & Fixes

#### Issue: Too Formal/Robotic

**Symptom:** Responses sound corporate, use filler phrases

**Fix in SOUL.md:**
```markdown
## Voice Anti-Patterns

Never use these phrases:
- "I'd be happy to help with that"
- "Great question!"
- "Certainly!"
- "I understand you're asking about..."
- "Thank you for reaching out"

Instead, just... help. Skip the preamble.

Example fix:
❌ "Great question! I'd be happy to help you with that. Your order status is..."
✅ "Your order shipped yesterday. Tracking: ABC123."
```

#### Issue: Not Following Procedures

**Symptom:** Skipping steps, inconsistent workflow

**Fix in AGENTS.md:**
```markdown
## [Workflow] - CRITICAL STEPS

⚠️ These steps are REQUIRED in this order:

1. **[Step 1]** - MUST complete before proceeding
   - [Specific action]
   - [Verification criteria]

2. **[Step 2]** - MUST complete before proceeding
   - [Specific action]
   - [Verification criteria]

DO NOT skip steps. DO NOT combine steps.
```

#### Issue: Breaking Boundaries

**Symptom:** Doing things outside defined scope

**Fix in SOUL.md:**
```markdown
## Hard Boundaries - NO EXCEPTIONS

These are not guidelines. These are rules:

1. **[Boundary]** - If asked, respond with:
   > "[Exact response to use]"

2. **[Boundary]** - If asked, respond with:
   > "[Exact response to use]"

If you're unsure whether something crosses a boundary, it does.
```

#### Issue: Wrong Tone in Specific Situations

**Symptom:** Inappropriate response in certain contexts

**Fix in SOUL.md:**
```markdown
## Situation-Specific Tone

### When user is upset
Switch from [normal tone] to [adjusted tone]:
- Lead with acknowledgment
- Shorter sentences
- Action-focused
- Example: "[Show exact wording]"

### When topic is sensitive
Switch from [normal tone] to [adjusted tone]:
- More formal
- Extra careful with phrasing
- Offer alternatives
- Example: "[Show exact wording]"
```

#### Issue: Not Using Skills Correctly

**Symptom:** Wrong tool for the job, or not using tools at all

**Fix in AGENTS.md:**
```markdown
## Tool Selection Guide

### For [type of request]:
1. First, check [local source]
2. If not found, use [specific skill]
3. If skill fails, [fallback]

### For [type of request]:
1. ALWAYS use [skill] - do not guess
2. [Specific parameters to use]

### Common Mistake
❌ Trying to answer from memory when you should look up
✅ Say "Let me check that" and use the appropriate skill
```

### 5.4 Iterative Refinement

After each test round:

1. **Identify patterns** - What fails repeatedly?
2. **Trace to source** - Which file controls that behavior?
3. **Make targeted edits** - Change only what's needed
4. **Retest specific cases** - Verify the fix
5. **Check for regressions** - Ensure fix didn't break other things

#### Refinement Log Template

Keep a log in your workspace:

```markdown
# REFINEMENT-LOG.md

## [Date] - Round 1

### Issues Found
1. [Issue description]
   - File: [which .md file]
   - Fix: [what was changed]
   - Status: [fixed/in progress]

2. [Issue description]
   - File: [which .md file]
   - Fix: [what was changed]
   - Status: [fixed/in progress]

### Tests Passed
- [List of passing tests]

### Next Steps
- [What to test next]
```

---

## Complete Examples

### Example 1: Minimal FAQ Bot

For a simple information-lookup assistant:

**SOUL.md:**
```markdown
# SOUL.md - FAQ Bot

## Core Identity
You answer frequently asked questions about [Topic]. You're helpful,
accurate, and concise. If you don't know something, you say so.

## Boundaries

### You handle:
- Questions covered in your knowledge base
- Directing people to the right resources

### You don't handle:
- Account-specific issues
- Anything requiring action beyond information

### When you don't know:
"I don't have information about that. You can reach our team at [contact]."
```

**AGENTS.md:**
```markdown
# AGENTS.md - FAQ Bot

## Response Flow
1. Check if question matches FAQ knowledge
2. If yes: provide answer
3. If no: direct to appropriate resource

## Knowledge
All answers are in `knowledge/faq.md`

## Response Style
- One answer per question
- Keep it under 3 sentences when possible
- Link to more info if available
```

### Example 2: Full-Featured Support Agent

See [JOB-CHARACTER-STRATEGIES.md](./JOB-CHARACTER-STRATEGIES.md) for complete
IT Help Desk, Hotel Concierge, and Retail Customer Service implementations.

---

## Troubleshooting Guide

### Quick Diagnosis

| Symptom | Likely Cause | Check |
|---------|--------------|-------|
| Wrong personality | SOUL.md not loaded | Session startup in AGENTS.md |
| Doesn't know things | Knowledge not linked | AGENTS.md knowledge sources |
| Can't do actions | Skills not configured | openclaw.json skills list |
| Does forbidden things | Tools not restricted | openclaw.json tools.deny |
| Inconsistent behavior | Conflicting instructions | Review all .md files for conflicts |
| Too verbose | No verbosity guidelines | Add examples to SOUL.md |
| Wrong escalation | Escalation rules unclear | Update AGENTS.md matrix |

### File Load Order

Understanding when files load helps diagnose issues:

1. **Always loaded first:** SOUL.md, IDENTITY.md
2. **Loaded in main session:** MEMORY.md, USER.md
3. **Loaded on reference:** knowledge/* files
4. **Loaded on need:** SKILL.md files
5. **Never loaded in groups:** MEMORY.md, USER.md

### Debug Checklist

When something's wrong:

- [ ] Did the session start correctly? (Check startup ritual)
- [ ] Is the right agent selected? (Check routing config)
- [ ] Are files in the right location? (Check workspace path)
- [ ] Are skills available? (Check skills list in config)
- [ ] Are tools allowed? (Check tools.allow/deny)
- [ ] Is there a conflicting instruction? (Search all .md files)
- [ ] Is the behavior defined at all? (May need to add it)

---

## Summary: The Key Files

| File | Purpose | Key Question It Answers |
|------|---------|------------------------|
| **SOUL.md** | Identity & boundaries | "Who am I?" |
| **AGENTS.md** | Procedures & operations | "What do I do?" |
| **IDENTITY.md** | Metadata | "What's my name?" |
| **USER.md** | User context | "Who am I helping?" |
| **TOOLS.md** | Tool configuration | "How do I use my tools?" |
| **MEMORY.md** | Long-term memory | "What do I remember?" |
| **knowledge/** | Domain facts | "What do I know?" |
| **SKILL.md** | Skill definitions | "What can I do?" |
| **openclaw.json** | System config | "What am I allowed to do?" |

Master these files, and you can build any assistant you need.
