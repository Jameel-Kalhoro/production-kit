# Examples — testing the standard in another project

These are scenarios to confirm the standard works once installed (see the repo
README for install steps).

## 1. Audit an existing repo

In any project you have locally, ask Claude Code:

> Use the production-auditor to audit this repo for production readiness.

Expect:

- A one-paragraph verdict naming the product type (plain web vs AI-based) and a
  count of High / Med / Low gaps.
- A table: `Concern | Status | Evidence | Severity | Suggested fix`, where every ❌
  cites what was searched for and not found (e.g. "no rate-limit layer in `src/api/`").
- A **Top fixes by severity** list, and an offer to apply fixes on request.
- **No files changed** — the auditor is read-only until you ask it to fix something.

## 2. Audit an AI product

Point it at a repo that uses agents/tools/pipelines. In addition to the backend and
testing concerns, expect it to walk the 5-level AI pyramid in reverse and flag any
missing level:

- an eval for each **tool**,
- an eval for each **agent**,
- a **tool↔agent contract** test (malformed tool output),
- a **whole-pipeline** e2e eval with latency/cost budgets,
- **multi-pipeline** handoff/isolation tests.

## 3. Build with the skill

Ask Claude Code to build a small API or AI feature. The `production-web` skill
should:

1. classify the product,
2. produce a decision table (throttling, validation, etc.) with no silent defaults,
3. implement against those decisions,
4. produce the required test artifacts,
5. finish with a ✅/⚠️/❌ self-audit citing `file:line` and test names.

## What "good" looks like

The auditor's report should be **specific** — file paths and line numbers, not
adjectives — and the skill's self-audit should make every skipped concern visible
with a stated reason. If either is vague, that's a signal the checklists need
deeper **Audit signals** / decision templates (see the roadmap in `DESIGN.md`).
