# Enforced build workflow

This is the gate. Walk it in order. The self-audit (step 5) is the proof it held.

## Step 1 — Classify the product

Decide and state one of:

- **Plain web app / API** — no LLM calls, no agents, no tool-calling.
- **AI-based product** — uses any of: LLM calls, agents, tool-calling, RAG,
  multi-step pipelines, multiple pipelines communicating.

If AI-based, `checklists/testing-ai.md` is **mandatory** in addition to the
standard testing checklist.

## Step 2 — Scope the checklists

Read the applicable checklist files. Produce a short table:

| Concern | Applies? | Explicit decision |
|---|---|---|
| Throttling | yes | token-bucket, per-user, 100/min, 429 + Retry-After |
| Input validation | yes | schema-validate every handler at the boundary |
| ... | ... | ... |

Every "yes" needs a concrete decision. "no" needs a one-line reason.

## Step 3 — Implement

Build against the decisions from step 2. Keep the decisions visible in code
(comments/config), not buried.

## Step 4 — Produce required test artifacts

From `testing-standard.md` (all products) and `testing-ai.md` (AI products).
You may not skip a category; a non-applicable category gets a one-line reason.

For AI products, the five levels are each required where they exist in the system:
single tool, single agent, tool↔agent contract, whole-pipeline, multi-pipeline.

## Step 5 — Self-audit

Walk every scoped item. Report:

| Concern | Status | Evidence | Note |
|---|---|---|---|
| Throttling | ✅ | `api/mw/rateLimit.ts:14`, test `rateLimit.spec:‘429 over budget’` | |
| Tool↔agent contract | ⚠️ | `evals/tool_contract.test` covers 2 of 3 tools | 3rd tool pending |

Close with an explicit list of every ❌/⚠️ and whether it is intentional.
