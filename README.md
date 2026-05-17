# Seshmark Agent Blame — AI Report for PRs

> Auto-generated AI attribution report on every Pull Request. See which AI agents wrote what, which models were used, and which files have the most AI code.

## Usage

Add this workflow to any repo:

```yaml
# .github/workflows/seshmark.yml
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

That's it. Every PR now gets an AI attribution report.

## Report Example

```markdown
## 🤖 Seshmark AI Report

| Metric | Value |
|--------|-------|
| AI Commits | 10 of 12 (83%) |
| Agents Used | cursor (7), claude (2), copilot (1) |
| Models | claude-sonnet-4, gpt-4o |
| Files with AI Code | src/auth.ts (100%), src/api.ts (78%) |
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `token` | Yes | `${{ github.token }}` | GitHub token for posting comments |
| `show-models` | No | `true` | Include model names in the report |
| `show-sessions` | No | `false` | Include session IDs in the report |
| `fail-on-unknown-agent` | No | `false` | Fail the check if an unapproved AI agent is detected |

## Outputs

| Output | Description |
|--------|-------------|
| `ai-percent` | Percentage of AI commits in this PR |
| `agents-found` | Comma-separated list of AI agents detected |

## How It Works

1. Installs the [seshmark CLI](https://github.com/seshmark/seshmark)
2. Runs `seshmark stats` and `seshmark query` on the PR's commits
3. Formats the data into a markdown table
4. Posts (or updates) a comment on the PR

Works with any AI coding tool that uses Seshmark's commit trailers: Cursor, Claude Code, Copilot, Pi, Aider, OpenCode, and more.

## License

MIT &copy; Seshmark Contributors
