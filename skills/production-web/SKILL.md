---
name: production-web
description: >
  Build web applications (and AI-based products — agents, tools, pipelines) to a
  production standard. Enforces a gated workflow covering backend robustness
  (throttling, validation, idempotency, pooling, caching, error handling),
  testing (unit/integration/e2e AND AI evals: single tool, single agent,
  tool↔agent contracts, whole-pipeline, multi-pipeline), and frontend quality
  (a11y, loading/error/empty states, validation, responsive). Stack-agnostic.
  Use when building or scaffolding a web app, backend API, or AI product, or when
  asked to make something "production-ready". For AUDITING existing code, use the
  production-auditor subagent instead (it reads the same checklists).
---

# production-web

You are building to a production standard. This skill is a **gate**: you may not
report the work as "done" until you have walked the workflow below and produced a
self-audit. Skipping a required concern is only allowed if you state *why* it does
not apply.

The engineering standards live in the shared knowledge base — the `checklists/`
directory alongside this file. Read the ones that apply before you plan:

- `checklists/backend-robustness.md`
- `checklists/testing-standard.md`
- `checklists/testing-ai.md`  ← required whenever the product is AI-based
- `checklists/frontend-quality.md`
- `checklists/ops-readiness.md`

Decision templates live in `templates/` (e.g. `templates/rate-limiter.principles.md`,
`templates/eval-harness.principles.md`).

## Enforced workflow

Follow `workflow.md`. In short:

1. **Classify the product.** Plain web app, or AI-based (uses agents / tools /
   pipelines / LLM calls)? If AI-based, `testing-ai.md` becomes mandatory.
2. **Scope the checklists.** List every concern that applies. For each, state the
   **explicit decision** you are making (e.g. "throttling: token-bucket, per-user,
   100 req/min, respond 429 + Retry-After"). No silent defaults.
3. **Implement** against those decisions.
4. **Produce the required test artifacts.** You may not skip a test category. If a
   category does not apply, write one line saying why.
5. **Self-audit.** Walk every scoped checklist item and report it as ✅ / ⚠️ / ❌
   with evidence (`file:line`, test name). End with the list of anything ❌/⚠️ and
   whether it's intentional.

## Rules

- Stack-agnostic: adapt every principle to the project's actual stack; never
  invent a stack the project isn't using.
- Make decisions **explicit and visible** — the point is to stop silent skipping.
- The self-audit is not optional. It is the deliverable that proves the gate held.
