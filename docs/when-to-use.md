# When to use which action

A problem-first guide to the AI DevOps Actions suite.

---

## "My AI outputs regressed and I don't know why"

**Stack: AI Debugging**

```
ai-workflow-evals → llm-cost-tracker → ai-root-cause-hints
```

| You have | Use |
|----------|-----|
| Eval suite that used to pass | [ai-workflow-evals](../examples/ai-debugging-stack.yml) |
| Costs that suddenly spiked | llm-cost-tracker |
| A RAG corpus that changes | rag-change-detector (pair with ai-root-cause-hints) |
| Any of the above | [ai-root-cause-hints](https://github.com/ollieb89/ai-root-cause-hints) to correlate them |

→ [See the AI Debugging Stack example](../examples/ai-debugging-stack.yml)

---

## "My PRs feel noisy and AI-sloppy"

**Stack: PR Hygiene**

```
pr-context-enricher → ai-pr-guardian → actions-lockfile-generator
```

| You have | Use |
|----------|-----|
| PRs with no context for reviewers | [pr-context-enricher](https://github.com/ollieb89/pr-context-enricher) |
| Suspected AI-generated PRs slipping through | [ai-pr-guardian](https://github.com/ollieb89/ai-pr-guardian) |
| Unpinned action references in workflows | [actions-lockfile-generator](https://github.com/ollieb89/actions-lockfile-generator) |

→ [See the PR Hygiene Stack example](../examples/pr-hygiene-stack.yml)

---

## "I'm worried about leaking secrets or PII"

**Stack: AI Safety**

```
ai-output-redacter → actions-lockfile-generator → ai-root-cause-hints
```

| You have | Use |
|----------|-----|
| AI-generated outputs that may contain secrets | [ai-output-redacter](https://github.com/ollieb89/ai-output-redacter) |
| Third-party actions that could be compromised | [actions-lockfile-generator](https://github.com/ollieb89/actions-lockfile-generator) |
| Redaction hits you can't explain | [ai-root-cause-hints](https://github.com/ollieb89/ai-root-cause-hints) |

→ [See the AI Safety Stack example](../examples/ai-safety-stack.yml)

---

## "I'm shipping agent skills or MCP servers"

**Stack: Agent Infra**

```
agent-skill-validator → mcp-server-tester
```

| You have | Use |
|----------|-----|
| OpenClaw / Claude Code / Codex skill repos | [agent-skill-validator](https://github.com/ollieb89/agent-skill-validator) |
| MCP servers you want to validate in CI | [mcp-server-tester](https://github.com/ollieb89/mcp-server-tester) |

---

## "I want everything"

→ [See the full pipeline](../README.md#full-pipeline)

---

## Comparison: which action detects what

| Scenario | Primary action | Supporting action |
|----------|---------------|-------------------|
| AI-generated PR slop | ai-pr-guardian | pr-context-enricher |
| Missing PR context for reviewers | pr-context-enricher | — |
| Secret/PII leak in AI output | ai-output-redacter | ai-root-cause-hints |
| Compromised third-party action | actions-lockfile-generator | — |
| Behavioral regression in prompts | ai-workflow-evals | ai-root-cause-hints |
| RAG corpus drift causing eval fail | ai-workflow-evals + rag-change-detector | ai-root-cause-hints |
| Runaway LLM cost | llm-cost-tracker | ai-root-cause-hints |
| Agent skill schema violation | agent-skill-validator | — |
| MCP server regression | mcp-server-tester | — |
| Unknown failure root cause | ai-root-cause-hints | all signal sources |

---

## Minimum viable setup (3 actions)

If you're just getting started, this is the highest-leverage combination:

```yaml
- uses: ollieb89/pr-context-enricher@v1.0.0    # give reviewers context
- uses: ollieb89/ai-pr-guardian@v1.0.0          # gate slop
- uses: ollieb89/ai-root-cause-hints@v1.0.0     # explain failures
```

These three work on every PR, require only `GITHUB_TOKEN`, and produce immediate visible output.

---

## Quick diagnosis guide

Not sure where to start? Answer these:

1. **Did your evals just fail?** → ai-workflow-evals + ai-root-cause-hints
2. **Did a PR just merge and something broke?** → ai-pr-guardian + pr-context-enricher + ai-root-cause-hints
3. **Are you worried about what's in your AI outputs?** → ai-output-redacter
4. **Did costs spike?** → llm-cost-tracker + ai-root-cause-hints
5. **Is your MCP server behaving differently?** → mcp-server-tester
6. **Are you about to publish an agent skill?** → agent-skill-validator

# Sun Mar 22 09:17:44 PM CET 2026
