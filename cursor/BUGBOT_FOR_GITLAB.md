# Bugbot for GitLab

> AI code reviewer that scans Merge Requests for logic bugs, security issues, and edge cases.

## Setup

1. **Connect GitLab**: Go to [Cursor Dashboard](https://cursor.com/dashboard) → Integrations → Connect GitLab
2. **Enable Repo**: In the Bugbot tab, toggle your project to Enabled
3. **Requirements**:
   - GitLab Premium or Ultimate plan (project access tokens required)
   - Developer access or higher on the repository

## How It Works

- **Trigger**: Runs automatically on every push to an open MR
- **Feedback**: Comments directly on problematic lines in GitLab
- **Scope**: Focuses on logic and intent, not syntax (linters handle that)

## Commands

Type these in any GitLab MR comment:

| Command | Purpose |
|---------|---------|
| `bugbot run` | Manually trigger a scan |
| `bugbot run verbose=true` | Trigger scan with Request ID for debugging |
| `cursor review` | Force a deep-dive review |

## Project Rules

Create `.cursor/BUGBOT.md` in your repo root to customize behavior:

```markdown
# Project Rules
- Flag any hardcoded secrets or API keys
- We use `date-fns`, not `moment.js`
- Ignore files in `/legacy` and `/docs`
- Ensure Postgres queries use parameterized inputs
```

Bugbot also finds nested `.cursor/BUGBOT.md` files by traversing upward from changed files.

## Autofix (Beta)

Bugbot can automatically fix issues it finds:

- **Create PRs**: Opens a fix PR for each issue
- **Push to Branch**: Pushes fixes directly (max 3 attempts per PR)

Configure in Cursor Dashboard → Bugbot → Autofix settings.

## Notes

- Included with Cursor Pro subscription
- Self-hosted GitLab supported via IP allowlisting or PrivateLink
- Free tier has limited monthly reviews

## Links

- [Bugbot Documentation](https://cursor.com/docs/bugbot)
- [Bugbot Configuration](https://docs.cursor.com/bugbot/configuration)
- [Cursor Dashboard](https://cursor.com/dashboard)
