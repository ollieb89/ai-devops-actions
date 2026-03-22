# AI DevOps Actions

> The CI/CD layer for AI-native development.

A suite of GitHub Actions that secure, validate, monitor, and control AI workflows — from PR quality gates to supply chain security.

## The Suite

| Action | What it does | Stars |
|--------|-------------|-------|
| [ai-pr-guardian](https://github.com/ollieb89/ai-pr-guardian) | Detect and score AI-generated / low-quality PRs | [![GitHub stars](https://img.shields.io/github/stars/ollieb89/ai-pr-guardian?style=flat-square)](https://github.com/ollieb89/ai-pr-guardian) |
| [llm-cost-tracker](https://github.com/ollieb89/llm-cost-tracker) | Track AI API costs in CI, alert on budget overruns | [![GitHub stars](https://img.shields.io/github/stars/ollieb89/llm-cost-tracker?style=flat-square)](https://github.com/ollieb89/llm-cost-tracker) |
| [mcp-server-tester](https://github.com/ollieb89/mcp-server-tester) | Test MCP servers in CI: health, compliance, discovery | [![GitHub stars](https://img.shields.io/github/stars/ollieb89/mcp-server-tester?style=flat-square)](https://github.com/ollieb89/mcp-server-tester) |
| [actions-lockfile-generator](https://github.com/ollieb89/actions-lockfile-generator) | Pin GitHub Actions to SHA, prevent supply chain attacks | [![GitHub stars](https://img.shields.io/github/stars/ollieb89/actions-lockfile-generator?style=flat-square)](https://github.com/ollieb89/actions-lockfile-generator) |
| [agent-skill-validator](https://github.com/ollieb89/agent-skill-validator) | Lint and validate agent skill repos (OpenClaw, Claude Code, Codex) | [![GitHub stars](https://img.shields.io/github/stars/ollieb89/agent-skill-validator?style=flat-square)](https://github.com/ollieb89/agent-skill-validator) |

## Full Pipeline Example

```yaml
jobs:
  ai-devops:
    runs-on: ubuntu-latest
    steps:
      # 1. Gate low-quality / AI-slop PRs
      - uses: ollieb89/ai-pr-guardian@v1
        with:
          threshold: 60
          on-low-quality: comment

      # 2. Pin all workflow action refs to SHA
      - uses: ollieb89/actions-lockfile-generator@v1
        with:
          mode: enforce
          github-token: ${{ secrets.GITHUB_TOKEN }}

      # 3. Track AI API costs from this run
      - uses: ollieb89/llm-cost-tracker@v1
        with:
          provider: anthropic
          model: claude-sonnet-4
          input-tokens: ${{ steps.ai-step.outputs.input-tokens }}
          output-tokens: ${{ steps.ai-step.outputs.output-tokens }}
          budget-limit: '0.50'

      # 4. Test your MCP server
      - uses: ollieb89/mcp-server-tester@v1
        with:
          transport: stdio
          server-command: "node dist/server.js"

      # 5. Validate agent skills
      - uses: ollieb89/agent-skill-validator@v1
        with:
          ecosystem: auto
          fail-on: errors
```

## Why this suite?

AI-native repos have new CI/CD needs that standard tooling doesn't cover:
- PRs flooded with AI-generated slop
- Unchecked LLM spend in pipelines  
- MCP servers shipped without protocol validation
- Action tags silently compromised via supply chain attacks
- Agent skills published without schema validation

This suite covers the full stack.

## Install

Each action is standalone. Use one, some, or all:

```yaml
uses: ollieb89/ai-pr-guardian@v1
uses: ollieb89/llm-cost-tracker@v1
uses: ollieb89/mcp-server-tester@v1
uses: ollieb89/actions-lockfile-generator@v1
uses: ollieb89/agent-skill-validator@v1
```

## Built by

[ollieb89](https://github.com/ollieb89) — shipping developer tools for AI-native teams.

---

*Part of the AI DevOps Actions suite. Each tool is MIT licensed, independently versioned, and production-ready.*
