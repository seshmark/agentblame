# Seshmark Agent Blame — AI Report for Every PR

> After installing this action, every Pull Request in your repo gets an automated comment showing which AI agents wrote what, which models were used, and which files have the most AI code.

[![CI](https://github.com/seshmark/agentblame/actions/workflows/ci.yml/badge.svg)](https://github.com/seshmark/agentblame/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

---

## Setup (30 seconds)

Add a file at `.github/workflows/seshmark.yml` in your repo with this content:

```yaml
name: Seshmark AI Report
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  report:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: seshmark/agentblame@v1
```

Commit and push. That's it.

Every new Pull Request will now get a comment like this:

```markdown
## 🤖 Seshmark AI Report

| Metric | Value |
|--------|-------|
| AI Commits | 10 of 12 (83%) |
| Agents Used | cursor (7), claude (2), copilot (1) |
| Models | claude-sonnet-4, gpt-4o |
| Files with AI Code | src/auth.ts (100%), src/api.ts (78%) |
```

---

## Prerequisites

This action works with any repo that has commits tagged with Seshmark trailers. If you or your team use AI coding tools (Cursor, Claude Code, Copilot, Pi, Aider, OpenCode, etc.), install the [seshmark CLI](https://github.com/seshmark/seshmark) once to start tagging commits automatically:

```bash
curl -fsSL https://seshmark.github.io/seshmark/install.sh | bash
```

Already using seshmark? You're all set. The action reads the trailers your commits already have.

---

## Configuration

### Inputs

| Input | Required | Default | What it does |
|-------|----------|---------|-------------|
| `token` | Yes | `${{ github.token }}` | GitHub token used to post the comment. Usually you don't need to change this. |
| `show-models` | No | `true` | Set to `false` to hide the model names column in the report. |
| `show-sessions` | No | `false` | Set to `true` to include session IDs in the commit table. |
| `fail-on-unknown-agent` | No | `false` | Set to `true` to fail the PR check if an unapproved AI agent is detected. |

### Outputs

| Output | What it contains |
|--------|-----------------|
| `ai-percent` | Percentage of commits in this PR that were AI-assisted (e.g., `83.3`) |
| `agents-found` | Comma-separated list of AI agents detected (e.g., `cursor, claude`) |

### Example with custom options

```yaml
- uses: seshmark/agentblame@v1
  with:
    show-sessions: true
    fail-on-unknown-agent: true
```

---

## How It Works

When a Pull Request is opened or updated, this action:

1. **Installs the seshmark CLI** in the runner (the same CLI developers install locally)
2. **Runs `seshmark stats`** on all commits in the PR to get summary data
3. **Runs `seshmark query`** to get per-commit details
4. **Formats the data** into a clean markdown table
5. **Posts the comment** on your PR. If a previous Seshmark comment exists, it updates it instead of creating a new one.

The action runs entirely inside GitHub's infrastructure. No data leaves the runner. No external APIs are called.

---

## What Makes a Good Report?

The action reads Seshmark commit trailers (`Seshmark-Version`, `AI-Agent`, `AI-Session`, `AI-Model`). These are added automatically by the seshmark CLI when:

- Your AI tool sets `SESHMARK_AGENT` and `SESHMARK_SESSION_ID` environment variables
- You ran `seshmark track` before the AI tool made commits
- The branch name follows the pattern `<agent-name>/feature` (e.g., `cursor/auth-refactor`)

The more metadata your commits have, the richer the report. At minimum, `AI-Agent` is needed for the agent breakdown. `AI-Model` adds the model column.

---

## Troubleshooting

**The comment didn't appear.** Check that:
- The workflow file is at `.github/workflows/seshmark.yml` (exact path matters)
- The repo has commits with Seshmark trailers (`git log --grep="AI-Agent"` should show results)
- The `permissions` block in the workflow includes `pull-requests: write`

**The report shows 0% AI commits.** This means none of the commits in the PR have Seshmark trailers. Install the [seshmark CLI](https://github.com/seshmark/seshmark) and make a few AI-assisted commits first.

**The report failed with "Could not determine PR commits".** This action only runs on `pull_request` events. Make sure the `on:` block in your workflow includes `pull_request`.

---

## Links

- [seshmark CLI repo](https://github.com/seshmark/seshmark) — the CLI that tags your commits
- [ADD_A_HARNESS.md](https://github.com/seshmark/seshmark/blob/main/ADD_A_HARNESS.md) — how to add support for any AI tool
- [HARNESS_EXAMPLES.md](https://github.com/seshmark/seshmark/blob/main/HARNESS_EXAMPLES.md) — per-tool setup guides

## License

MIT &copy; Seshmark Contributors
