# Launch Posts

> **Canonical demo PR:** [PR #1](https://github.com/ollieb89/ai-devops-actions/pull/1) — shows full suite output (context summary, quality score, root cause hints). Or browse [all recent PRs](https://github.com/ollieb89/ai-devops-actions/pulls) for consistency proof.

## Hacker News — Show HN

**Title:**
Show HN: AI DevOps Actions – 9 GitHub Actions for CI/CD in AI-native repos

**Post body:**
---
I've been building developer tools for AI-native repos — codebases where LLMs generate code, RAG pipelines drift, prompts regress, and CI needs to catch it before production does.

Standard CI/CD tooling doesn't cover this. So I built a suite of 9 GitHub Actions that handle the full loop:

**The problem stack:**
- PRs flooded with AI-generated slop → ai-pr-guardian (scores 0–100, gates on threshold)
- Reviewers lack context on AI changes → pr-context-enricher (risk level, commit summary, AI reviewer prompt)
- Secrets leaking through AI outputs → ai-output-redacter (scans for keys, PII, JWTs)
- Action tags silently compromised → actions-lockfile-generator (pins all `uses:` to full SHAs)
- Behavioral regressions invisible → ai-workflow-evals (Jest-style evals for prompts and agents)
- MCP servers untested → mcp-server-tester (health + protocol compliance in CI)
- Agent skills published without schema checks → agent-skill-validator
- LLM spend unchecked → llm-cost-tracker (budget alerts per run)
- Failures with no explanation → ai-root-cause-hints (correlates signals, explains *why* it broke)

**The last one is the interesting one.** It's purely rule-based — no LLM calls. It reads signals from the other actions and produces a "likely cause" with confidence level (HIGH/MEDIUM/LOW) and suggested actions.

Example output when evals fail after a RAG corpus change:
```
❌ Eval failure likely caused by knowledge drift
Confidence: HIGH
Signals: eval_failure + rag_change (18% deletion in /docs/support/)
→ Review deleted documents in /knowledge-base
→ Re-run evals with previous corpus snapshot
→ Pin corpus version or add a change gate before eval runs
```

**This repo dogfoods itself** — every PR runs the suite. Open any recent PR to see what the output looks like before you set it up.

**1-minute setup** (no external API, no LLM key required):
```yaml
- uses: ollieb89/pr-context-enricher@v1.0.0
- uses: ollieb89/ai-pr-guardian@v1.0.0
- uses: ollieb89/ai-root-cause-hints@v1.0.0
```

GitHub: https://github.com/ollieb89/ai-devops-actions

Happy to answer questions about the architecture — especially the correlation engine design. Curious whether these failure patterns match what you're seeing in production.

---

**Tags:** github-actions, devops, ai, llm, developer-tools

---

## Twitter/X — Thread

**Tweet 1 (hook):**
I built 9 GitHub Actions for AI-native repos.

The most interesting one doesn't call an LLM.

It just reads signals from the others and tells you *why* your pipeline broke. 🧵

---

**Tweet 2 (the problem):**
Standard CI catches build failures.

AI-native repos have different problems:
- evals that silently regress
- RAG corpora that drift
- prompts that change behavior without breaking syntax
- costs that spike from a single bad merge

None of this shows up in your test suite.

---

**Tweet 3 (the solution):**
So I built a suite of 9 GitHub Actions that covers the full loop:

🔍 PR Quality & Context
🛡️ Safety & Security
🧪 Testing & Behavioral Validation
💰 Infra & Cost

Each works standalone. Together, they form a complete debugging layer.

---

**Tweet 4 (the interesting part):**
The 9th action is ai-root-cause-hints.

It's rule-based. No LLM. No API key.

It reads eval failures, RAG changes, cost spikes, and PR risk signals — then correlates them.

Example:

❌ Eval failure likely caused by knowledge drift
Confidence: HIGH
→ 18% of your RAG corpus was deleted
→ Re-run evals with the previous snapshot

---

**Tweet 5 (proof):**
This repo dogfoods itself.

Every PR runs the suite.

Open any recent PR: https://github.com/ollieb89/ai-devops-actions/pulls

You'll see the context summary, quality score, and root cause hints — all from a single GITHUB_TOKEN.

---

**Tweet 6 (CTA):**
1-minute setup. No external API. No LLM key.

```yaml
- uses: ollieb89/pr-context-enricher@v1.0.0
- uses: ollieb89/ai-pr-guardian@v1.0.0
- uses: ollieb89/ai-root-cause-hints@v1.0.0
```

Full suite: https://github.com/ollieb89/ai-devops-actions

441 tests. MIT. Go build something real.

---

## Reddit — r/devops or r/MachineLearning

**Title:**
I built a debugging layer for AI pipelines using GitHub Actions — explains *why* your evals failed, not just that they did

**Body:**
---
Been building AI-native apps for a while and kept running into the same problem: standard CI tells me *what* broke, but not *why*.

An eval fails. Is it a prompt regression? A RAG corpus drift? A dependency upgrade that changed tokenization? Without correlation across signals, you're just guessing.

So I built **ai-root-cause-hints** — a GitHub Action that reads signals from your other CI steps and produces root cause hints with confidence levels.

**How it works:**

It's completely rule-based (no LLM calls). A small set of deterministic correlation rules:

- `eval_failure` + `rag_change` → "Knowledge drift likely cause" (HIGH confidence when >10% deletion)
- `cost_spike` + `low_pr_quality` → "Bad prompt likely landed"
- `redaction_hit` + `high_pr_risk` → "Sensitive data leak from risky change"
- `eval_failure` alone → "Prompt regression"
- etc.

**Example output:**
```
❌ Eval failure likely caused by knowledge drift
Confidence: HIGH
Signals used: ai-workflow-evals/eval_failure, rag-change-detector/rag_change
→ Review deleted documents in /knowledge-base
→ Re-run evals with previous corpus snapshot
→ Pin corpus version or add a change gate before eval runs
```

**This is part of a 9-action suite** that covers the full AI DevOps loop — PR quality, output safety, behavioral evals, cost tracking, supply chain security, and now root cause analysis.

The repo dogfoods itself (PRs run the suite), so you can see real output without setting anything up: https://github.com/ollieb89/ai-devops-actions/pulls

1-minute setup, no API key required, MIT license.

What am I missing? Genuinely curious whether the correlation rules match the failure modes you actually hit in production.

---

## LinkedIn — Short post

**Post:**
I've spent the last few weeks building something I wish existed when I started working with AI pipelines.

When your evals fail, you don't just want to know *that* they failed. You want to know *why*.

Was it a RAG corpus change? A prompt regression? A dependency update that changed behavior?

I built **ai-root-cause-hints** — a GitHub Action that correlates signals from your eval runs, cost tracker, PR analysis, and output scanner, then produces actionable root cause hints with confidence levels.

No LLM calls. No API key. Pure rule-based correlation, CI-native.

It's the 9th action in the AI DevOps Actions suite — 9 GitHub Actions for the full CI/CD lifecycle of AI-native repos.

The repo dogfoods itself. Every PR runs the suite. Open a recent PR to see it in action before you set anything up.

→ https://github.com/ollieb89/ai-devops-actions

1-minute setup. MIT. Feedback welcome.

---

## Dev.to — Article title options

1. "I built a debugging layer for AI pipelines: 9 GitHub Actions that explain *why* your evals failed"
2. "Show HN but for Dev.to: AI DevOps Actions — CI/CD for AI-native repos"
3. "Why standard CI/CD doesn't work for AI repos (and what I built instead)"

