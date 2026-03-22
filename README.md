# AI DevOps Actions

> The CI/CD layer for AI-native development.

AI-native repos have new problems that standard CI/CD tooling doesn't cover. PRs flooded with AI slop. Unchecked LLM spend. MCP servers shipped without validation. Action tags silently compromised. Agent skills published without schema checks.

This suite covers the full stack — five GitHub Actions that work independently or together.

---

## The Suite

| Action | What it solves |
|--------|---------------|
| [**ai-pr-guardian**](https://github.com/ollieb89/ai-pr-guardian) | Scores PR quality 0–100, detects AI-generated slop, gates merges |
| [**llm-cost-tracker**](https://github.com/ollieb89/llm-cost-tracker) | Tracks OpenAI/Anthropic/Gemini spend in CI, alerts on budget overruns |
| [**mcp-server-tester**](https://github.com/ollieb89/mcp-server-tester) | Validates MCP servers: health, protocol compliance, tool/resource discovery |
| [**actions-lockfile-generator**](https://github.com/ollieb89/actions-lockfile-generator) | Pins all `uses:` to full SHAs — prevents supply chain attacks |
| [**agent-skill-validator**](https://github.com/ollieb89/agent-skill-validator) | Lints and validates agent skill repos (OpenClaw, Claude Code, Codex, Gemini) |

---

## Full Pipeline

```yaml
jobs:
  ai-devops:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # Gate AI-generated / low-quality PRs before review
      - uses: ollieb89/ai-pr-guardian@v1
        with:
          threshold: 60
          on-low-quality: comment

      # Enforce SHA pinning on all workflow actions
      - uses: ollieb89/actions-lockfile-generator@v1
        with:
          mode: enforce
          github-token: ${{ secrets.GITHUB_TOKEN }}

      # Track what this run cost in LLM calls
      - uses: ollieb89/llm-cost-tracker@v1
        with:
          provider: anthropic
          model: claude-sonnet-4
          input-tokens: ${{ steps.ai-review.outputs.input-tokens }}
          output-tokens: ${{ steps.ai-review.outputs.output-tokens }}
          budget-limit: '1.00'

      # Validate your MCP server didn't regress
      - uses: ollieb89/mcp-server-tester@v1
        with:
          transport: stdio
          server-command: "node dist/server.js"
          fail-on: errors

      # Validate agent skills before publish
      - uses: ollieb89/agent-skill-validator@v1
        with:
          ecosystem: auto
          fail-on: errors
```

---

## Install any action independently

```yaml
uses: ollieb89/ai-pr-guardian@v1
uses: ollieb89/llm-cost-tracker@v1
uses: ollieb89/mcp-server-tester@v1
uses: ollieb89/actions-lockfile-generator@v1
uses: ollieb89/agent-skill-validator@v1
```

Each action is MIT licensed, independently versioned, and production-ready with 48–81 tests.

---

## Built by [ollieb89](https://github.com/ollieb89)

Shipping developer tools for AI-native teams.

---

## Start here

Not sure where to start? Follow the onboarding path:

1. **[ai-pr-guardian](https://github.com/ollieb89/ai-pr-guardian)** — improve PR quality first
2. **[llm-cost-tracker](https://github.com/ollieb89/llm-cost-tracker)** — get visibility into AI spend
3. **[mcp-server-tester](https://github.com/ollieb89/mcp-server-tester)** — validate your MCP infra
4. **[actions-lockfile-generator](https://github.com/ollieb89/actions-lockfile-generator)** — lock down supply chain
5. **[agent-skill-validator](https://github.com/ollieb89/agent-skill-validator)** — ship validated agent skills

---

## Coming soon

| Action | Purpose |
|--------|---------|
| [**ai-workflow-evals**](https://github.com/ollieb89/ai-workflow-evals) | Run prompt/agent/workflow eval cases in CI — catch behavioral regressions before merge |
