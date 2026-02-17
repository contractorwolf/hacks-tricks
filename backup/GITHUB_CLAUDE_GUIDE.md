# Using Claude with GitHub

This guide explains how to install the Claude GitHub App and use `@claude` to request features, fixes, and code reviews directly in your GitHub issues and pull requests.

---

## Quick Start

### 1. Install the GitHub App

In a Claude Code session, run:

```
/install-github-app
```

Follow the interactive prompts to:
- Install the Claude GitHub App to your repository
- Configure your `ANTHROPIC_API_KEY` as a repository secret
- Set up GitHub Actions workflows

### 2. Tag Claude in Issues or PRs

Once installed, mention `@claude` anywhere in your repository:

```
@claude implement dark mode for the settings page
```

Claude will analyze your codebase and create a PR with the implementation.

---

## What the GitHub App Does

| Capability | Example |
|------------|---------|
| **Create PRs** | `@claude add user authentication` |
| **Fix bugs** | `@claude fix the null pointer in login.ts` |
| **Code review** | `@claude review this PR` |
| **Answer questions** | `@claude how does the caching system work?` |
| **Implement features** | `@claude add pagination to the API` |
| **Refactor code** | `@claude refactor this to use async/await` |

---

## Requirements

Before running `/install-github-app`:

- You must be a **repository administrator**
- You need an **Anthropic API key** (Claude Console or Pro subscription)
- **GitHub Actions** must be enabled on the repository
- Direct Claude API access (not AWS Bedrock or Google Vertex AI)

---

## Permissions Requested

The Claude GitHub App requires:

| Permission | Access | Purpose |
|------------|--------|---------|
| Contents | Read & Write | Read code, create commits |
| Issues | Read & Write | Respond to issues, create issues |
| Pull Requests | Read & Write | Create PRs, push changes, comment |

---

## How to Tag Claude with Feature Requests

Once the app is installed, create an issue or PR comment with `@claude` followed by your request.

### Feature Request Format

```markdown
@claude [description of what you want]

## Context (optional)
- Any relevant details
- File locations
- Constraints or requirements

## Acceptance Criteria (optional)
- [ ] Specific requirement 1
- [ ] Specific requirement 2
```

### Examples

**Simple feature:**
```
@claude add a logout button to the header component
```

**Detailed feature:**
```
@claude implement user settings page

## Requirements
- Create /settings route
- Include profile editing (name, email, avatar)
- Add password change form
- Use existing UI components from src/components/ui

## Acceptance Criteria
- [ ] Form validation on all fields
- [ ] Toast notifications for success/error
- [ ] Mobile responsive layout
```

**Bug fix:**
```
@claude fix the memory leak in useWebSocket hook

The connection isn't being cleaned up on unmount.
See src/hooks/useWebSocket.ts
```

**Code review:**
```
@claude review this PR

Focus on:
- Security issues
- Performance concerns
- Error handling
```

**Question:**
```
@claude how should I implement rate limiting for the API?

We're using Express and Redis is available.
```

---

## Best Practices

### Be Specific

| Less effective | More effective |
|----------------|----------------|
| `@claude fix the bug` | `@claude fix the TypeError on line 42 of auth.ts` |
| `@claude add tests` | `@claude add unit tests for the UserService class` |
| `@claude improve this` | `@claude refactor to reduce duplication in the validation logic` |

### Provide Context

- Reference specific files or functions
- Explain the current behavior vs expected behavior
- Mention any constraints (compatibility, dependencies)

### Use CLAUDE.md

Create a `CLAUDE.md` file in your repository root to define:
- Coding standards
- Project structure
- Preferred patterns
- Testing requirements

Claude will follow these guidelines automatically.

---

## Workflow Examples

### Automated Issue-to-PR

1. Create an issue describing a feature
2. Comment `@claude implement this`
3. Claude creates a PR with the implementation
4. Review and merge

### Code Review on Every PR

Add to `.github/workflows/claude-review.yml`:

```yaml
name: Claude Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Review this PR for bugs, security issues, and code quality"
```

### Feature Implementation

```yaml
name: Claude Feature
on:
  issue_comment:
    types: [created]

jobs:
  implement:
    if: contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Claude doesn't respond | Check GitHub Actions is enabled and API key is set |
| "Permission denied" | Verify you're a repository admin |
| Slow response | Large repos take longer; add `CLAUDE.md` to guide Claude |
| Wrong implementation | Be more specific in your request; add acceptance criteria |

---

## Security Notes

- Your code stays on GitHub's runners (never sent to external servers)
- API key is stored as an encrypted GitHub secret
- Claude respects repository permissions
- All changes are made via standard Git commits (auditable)

---

## Manual Setup (Alternative)

If `/install-github-app` doesn't work for your setup:

1. Go to https://github.com/apps/claude
2. Click "Install" and select your repository
3. Add `ANTHROPIC_API_KEY` to repository secrets (Settings → Secrets → Actions)
4. Create workflow files in `.github/workflows/`

See: https://github.com/anthropics/claude-code-action

---

## Example: Creating a Feature List for Claude

Create a GitHub issue with your feature requests:

```markdown
# Feature Requests for PocketGPT

@claude please implement these features:

## 1. Settings Persistence
Save user preferences (brightness, volume) to SD card so they persist across reboots.
- Location: `PocketGPT_System.cpp`
- Use existing SDCard module

## 2. Message Timestamps
Show timestamp on each message in the chat view.
- Format: "2:30 PM" for today, "Jan 15" for older
- Location: `PocketGPT_AppChatGPT.cpp`

## 3. Delete Confirmation
Add confirmation dialog before deleting a thread.
- Prevent accidental deletion
- Location: `PocketGPT_Display.cpp`

## 4. Battery Low Warning
Show toast notification when battery drops below 20%.
- Location: `PocketGPT_System.cpp`
- Use existing toast system

Please create separate PRs for each feature.
```

Claude will read this issue and create PRs implementing each feature.

---

*For more details, see the [Claude Code GitHub Actions documentation](https://docs.anthropic.com/en/docs/claude-code/github-actions).*
