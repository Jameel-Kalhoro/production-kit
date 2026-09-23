# Testing standard — AI-based products

Mandatory when the product uses LLM calls, tools, agents, or pipelines. AI outputs
are non-deterministic, so tests are **eval harnesses** (dataset of cases + a scorer),
not `===` assertions. Score by: structural checks (schema/format), rubric/LLM-judge,
and regression golden sets — not exact-match, except where output is deterministic.

Each concern below is written for BOTH consumers:
- **Required (build):** what must exist for this to be "done".
- **Audit signals:** what to look for to confirm it's present.
- **Failure modes / Severity:** what missing/broken looks like, and how bad.

---

## Level 1 — Single tool (in isolation)

- **Principle:** every tool is a pure-ish unit with a contract; test it alone before
  any agent uses it.
- **Required (build):** for each tool — valid input → correct output; invalid input
  → graceful, typed error (no crash); output conforms to declared schema;
  idempotency where claimed; side-effects mocked/asserted; timeout/failure path.
- **Audit signals:** a test/eval file per tool; assertions on error paths and schema,
  not just the happy path.
- **Failure modes:** only happy-path tested; errors bubble as raw exceptions; output
  schema drift unnoticed. **Severity: High.**

## Level 2 — Single agent (decision quality)

- **Principle:** an agent chooses tools and steps; test its judgment, not just its
  plumbing.
- **Required (build):** eval set where you know the right behavior — agent picks the
  correct tool; handles a tool returning an error; stays in scope / refuses when it
  should; does not loop forever (step budget). Score by rubric/judge.
- **Audit signals:** an eval dataset with labeled expected behavior; a scorer; a
  step/iteration cap under test.
- **Failure modes:** no eval set (only manual "looks good"); no test for tool-failure
  handling; no runaway-loop guard. **Severity: High.**

## Level 3 — Tool ↔ agent communication (the contract)

- **Principle:** the boundary between agent and tool is where most silent breakage
  lives.
- **Required (build):** agent passes arguments the tool actually accepts (validated
  against tool schema); agent correctly parses tool output; agent handles malformed /
  partial / empty tool responses without crashing or hallucinating success.
- **Audit signals:** contract tests that feed malformed tool responses to the agent;
  schema shared/validated on both sides.
- **Failure modes:** agent assumes tool output shape; no test for malformed response;
  arg mismatch only found at runtime. **Severity: High.**

## Level 4 — Whole pipeline (end-to-end, one pipeline)

- **Principle:** the pipeline is the product; test the full path a request takes.
- **Required (build):** golden-path eval set (representative inputs → expected
  outcomes by rubric); regression cases for every bug ever found; latency budget and
  cost budget asserted; failure-recovery path (a mid-pipeline step fails → graceful
  degradation, not total collapse).
- **Audit signals:** an e2e eval suite; a regression corpus that grows; explicit
  latency/cost assertions.
- **Failure modes:** no e2e evals; no cost/latency ceiling; no regression corpus so
  fixed bugs return. **Severity: High.**

## Level 5 — Multi-pipeline communication

- **Principle:** when pipelines call or feed each other, failures cascade unless the
  handoff is contracted and isolated.
- **Required (build):** handoff contract tested (output of A is valid input to B);
  backpressure / queue behavior under load; partial-failure isolation (B failing does
  not corrupt or hang A); no cascade failure (one pipeline down ≠ all down); tracing/
  correlation id across the boundary.
- **Audit signals:** integration tests across pipelines; a chaos/failure-injection
  test at the boundary; correlation ids in logs.
- **Failure modes:** pipelines coupled with no contract test; one failure cascades;
  no isolation or timeout at the handoff. **Severity: High.**

---

## Cross-cutting for AI products

- **Determinism control in tests:** pin model/seed/temperature where possible; when
  not possible, score by rubric and assert on distributions/thresholds, not one run.
- **Prompt regression:** changing a prompt must re-run the eval sets; treat prompts
  as code under test.
- **Cost & token budgets:** every eval run reports token/cost; CI fails on regression.
- **Safety / refusal evals:** adversarial inputs → correct refusal / no jailbreak,
  where the product has a safety boundary.
