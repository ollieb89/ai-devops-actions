# AI DevOps Actions

[![AI DevOps Actions Suite](https://img.shields.io/badge/AI%20DevOps%20Actions-Suite-blue?logo=github)](https://github.com/ollieb89/ai-devops-actions)
![Setup time](https://img.shields.io/badge/setup-1%20minute-brightgreen)
![No LLM required](https://img.shields.io/badge/LLM-not%20required-informational)
![No external API](https://img.shields.io/badge/external%20API-none-success)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)


> The full CI/CD layer for AI-native development — 8 GitHub Actions covering PR quality, safety, cost, infra, and behavioral testing.

AI-native repos have problems that standard CI/CD doesn't solve. PRs flooded with AI slop. Unchecked LLM spend. Sensitive data leaking through AI outputs. MCP servers shipped without validation. Action tags silently compromised. Agent skills published without schema checks. Behavioral regressions invisible until production.

This suite covers the full stack — eight GitHub Actions that work independently or as a pipeline.

---


---

## ⚡️ 1-minute setup

1. Copy this into `.github/workflows/ai-hygiene.yml` in any repo:

```yaml
name: AI PR Hygiene
on:
  pull_request:
    types: [opened, synchronize, reopened]
jobs:
  hygiene:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
      - uses: actions/checkout@v4
      - uses: ollieb89/pr-context-enricher@v1.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
      - uses: ollieb89/ai-pr-guardian@v1.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          threshold: 60
      - uses: ollieb89/ai-root-cause-hints@v1.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

2. Open a PR.

3. You'll see:

```
🔍 PR Context Summary
Author: @you | Base: main ← feature/my-change
5 files changed (+120/-30) | Risk: low | Complexity: 3/10
Related issues: #42

✅ PR Quality Score: 78/100

🟢 No correlated failure patterns detected.
```

No API keys. No external services. Just `GITHUB_TOKEN`.


---

## Start here

**New to the suite? Pick your entry point:**

| You want to... | Start with |
|----------------|-----------|
| Gate AI-generated PR slop | [ai-pr-guardian](https://github.com/ollieb89/ai-pr-guardian) |
| Give AI reviewers full PR context | [pr-context-enricher](https://github.com/ollieb89/pr-context-enricher) |
| Stop secrets leaking from AI outputs | [ai-output-redacter](https://github.com/ollieb89/ai-output-redacter) |
| Lock down your supply chain | [actions-lockfile-generator](https://github.com/ollieb89/actions-lockfile-generator) |
| Catch agent behavioral regressions | [ai-workflow-evals](https://github.com/ollieb89/ai-workflow-evals) |
| Validate your MCP server in CI | [mcp-server-tester](https://github.com/ollieb89/mcp-server-tester) |
| Publish agent skills safely | [agent-skill-validator](https://github.com/ollieb89/agent-skill-validator) |
| Understand why your AI pipeline broke | [ai-root-cause-hints](https://github.com/ollieb89/ai-root-cause-hints) |
| Track LLM spend before it hits your card | [llm-cost-tracker](https://github.com/ollieb89/llm-cost-tracker) |

Each action works standalone. The [full pipeline](#full-pipeline) shows how they compose.

## The Suite

### 🔍 PR Quality & Context

| Action | What it solves |
|--------|----------------|
| [**ai-pr-guardian**](https://github.com/ollieb89/ai-pr-guardian) | Scores PR quality 0–100, detects AI-generated slop, gates merges |
| [**pr-context-enricher**](https://github.com/ollieb89/pr-context-enricher) | Auto-generates rich context summaries: files, risk level, commit history, ready-to-paste AI reviewer prompt |

### 🛡️ Safety & Security

| Action | What it solves |
|--------|----------------|
| [**ai-output-redacter**](https://github.com/ollieb89/ai-output-redacter) | Scans and redacts API keys, tokens, PII, and secrets from AI-generated outputs before they leave CI |
| [**actions-lockfile-generator**](https://github.com/ollieb89/actions-lockfile-generator) | Pins all `uses:` to full commit SHAs — prevents supply chain attacks |

### 🧪 Testing & Behavioral Validation

| Action | What it solves |
|--------|----------------|
| [**ai-workflow-evals**](https://github.com/ollieb89/ai-workflow-evals) | Runs eval suites for prompts, agents, and workflows — catches behavioral regressions before merge |
| [**mcp-server-tester**](https://github.com/ollieb89/mcp-server-tester) | Validates MCP servers: health, protocol compliance, tool/resource discovery |
| [**agent-skill-validator**](https://github.com/ollieb89/agent-skill-validator) | Lints and validates agent skill repos (OpenClaw, Claude Code, Codex, Gemini) |

### 💰 Infrastructure & Cost

| Action | What it solves |
|--------|----------------|
| [**llm-cost-tracker**](https://github.com/ollieb89/llm-cost-tracker) | Tracks OpenAI/Anthropic/Gemini spend in CI, alerts on budget overruns |

---


---

## Usage paths — start with your problem

Don't know which action to use? Pick your problem:

| My problem | Recommended stack |
|------------|-------------------|
| AI outputs regressed, don't know why | [AI Debugging Stack](examples/ai-debugging-stack.yml) — evals + cost tracker + root cause hints |
| PRs are noisy and AI-sloppy | [PR Hygiene Stack](examples/pr-hygiene-stack.yml) — context enricher + guardian + lockfile |
| Worried about secrets leaking | [AI Safety Stack](examples/ai-safety-stack.yml) — output redacter + lockfile + root cause hints |
| Shipping agent skills or MCP servers | agent-skill-validator + mcp-server-tester |
| Want everything | [Full pipeline](#full-pipeline) |

→ [Detailed "when to use which action" guide](docs/when-to-use.md)

## Full Pipeline

```yaml
jobs:
  ai-devops:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # Enrich PR with context for AI reviewers
      - id: context
        uses: ollieb89/pr-context-enricher@v1.0.0
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}

      # Gate AI-generated / low-quality PRs before review
      - uses: ollieb89/ai-pr-guardian@v1.0.0
        with:
          threshold: 60
          on-low-quality: comment

      # Scan AI outputs for secrets and PII before they ship
      - uses: ollieb89/ai-output-redacter@v1.0.0
        with:
          path: ./outputs
          mode: enforce

      # Enforce SHA pinning on all workflow action references
      - uses: ollieb89/actions-lockfile-generator@v1.0.0
        with:
          mode: enforce
          github-token: ${{ secrets.GITHUB_TOKEN }}

      # Catch behavioral regressions in prompts and agents
      - uses: ollieb89/ai-workflow-evals@v1.0.0
        with:
          eval-suite: ./evals
          fail-on: regression

      # Track what this run cost in LLM calls
      - uses: ollieb89/llm-cost-tracker@v1.0.0
        with:
          provider: anthropic
          budget-limit: '1.00'

      # Validate MCP server didn't regress
      - uses: ollieb89/mcp-server-tester@v1.0.0
        with:
          transport: stdio
          server-command: "node dist/server.js"

      # Validate agent skills before publish
      - uses: ollieb89/agent-skill-validator@v1.0.0
        with:
          ecosystem: auto
          fail-on: errors
```

---

## Install any action independently

```yaml
# PR Quality & Context
uses: ollieb89/ai-pr-guardian@v1.0.0
uses: ollieb89/pr-context-enricher@v1.0.0

# Safety & Security
uses: ollieb89/ai-output-redacter@v1.0.0
uses: ollieb89/actions-lockfile-generator@v1.0.0

# Testing & Behavioral Validation
uses: ollieb89/ai-workflow-evals@v1.0.0
uses: ollieb89/mcp-server-tester@v1.0.0
uses: ollieb89/agent-skill-validator@v1.0.0

# Infrastructure & Cost
uses: ollieb89/llm-cost-tracker@v1.0.0
```

All actions are MIT licensed, independently versioned, and production-ready.

---

## Suite stats

| Layer | Actions | Total tests |
|-------|---------|-------------|
| PR Quality & Context | 2 | 86 |
| Safety & Security | 2 | 127 |
| Testing & Behavioral Validation | 4 | 180 |
| Infrastructure & Cost | 1 | 48 |
| **Total** | **9** | **441** |

---

## Related tools

- [**workflow-guardian**](https://github.com/ollieb89/workflow-guardian) — Workflow health monitoring and linting
- [**ghact**](https://github.com/ollieb89/ghact) — CLI toolkit: lint workflows, audit security, check for updates
- [**workflow-linter-vscode**](https://github.com/ollieb89/workflow-linter-vscode) — VS Code extension for real-time workflow linting
