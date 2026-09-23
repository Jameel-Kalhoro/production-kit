# AI eval harness — structure template

An eval harness = **dataset of cases** + **scorer** + **runner/report**. One per level
where the system has that level (see `../checklists/testing-ai.md`).

## Case shape
```
{ id, input, context?, expected|rubric, tags: [level, feature, regression?] }
```

## Scorer types (pick per case)
- **Structural** — schema/format/type conforms. Deterministic, cheap. Use first.
- **Rubric / LLM-judge** — grade against explicit criteria; judge prompt is versioned
  and itself spot-checked against human labels.
- **Golden / regression** — exact or near-exact match for cases that must not drift.
- **Threshold / distribution** — for non-deterministic output, assert pass-rate ≥ X
  over N runs, not a single run.

## Per-level focus
| Level | Dataset is... | Scorer emphasis |
|---|---|---|
| Single tool | valid + invalid + boundary inputs | structural + error-path |
| Single agent | scenarios with known-right behavior | rubric: right tool, in-scope, no loop |
| Tool↔agent | malformed/partial tool responses | agent doesn't crash / fake success |
| Whole pipeline | representative + regression inputs | rubric + latency/cost budget |
| Multi-pipeline | cross-handoff + failure-injection | contract valid, isolation, no cascade |

## Report must include
- pass/fail per case, aggregate pass-rate, token + cost total, latency p50/p95.
- CI fails on: pass-rate regression, cost/latency regression, any golden-case break.

## Rules
- Pin model/seed/temperature where the platform allows; otherwise use thresholds.
- Prompts are code: any prompt change re-runs the relevant eval sets.
- Regression corpus only grows — every production bug becomes a case.
