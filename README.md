# production-kit

A reusable production standard for AI-assisted development, packaged for
[Claude Code](https://claude.com/claude-code). It makes AI produce
production-grade software by default — instead of happy-path code that silently
skips throttling, validation, tests, and edge cases.

It ships two components that share one knowledge base:

- **`production-web`** (a skill) — gates *building* new work through a required
  workflow: classify → scope decisions → implement → test artifacts → self-audit.
- **`production-auditor`** (a subagent) — *audits* already-built code against the
  same standard and returns a scored gap report with evidence and severity.

Both read the same **dual-purpose checklists**, so how you build and how you grade
never drift apart. The standard is **stack-agnostic** — it encodes principles and
required outputs, and adapts to whatever stack your project uses. It explicitly
covers **AI products** (agents, tools, pipelines), not just plain web apps.

## What's in here

```
skills/production-web/   # the build skill (SKILL.md, workflow.md, checklists/, templates/)
agents/production-auditor.md   # the audit subagent
DESIGN.md                # the design document — read this to understand the system
examples/                # a scenario for testing the auditor in another project
```

## Install

Copy the two folders into your Claude Code config.

**Global (available in every project):**

```bash
cp -R skills/production-web ~/.claude/skills/
cp agents/production-auditor.md ~/.claude/agents/
```

**Project-local (checked into one project):**

```bash
mkdir -p .claude/skills .claude/agents
cp -R skills/production-web .claude/skills/
cp agents/production-auditor.md .claude/agents/
```

The `production-auditor` reads checklists from the installed `production-web` skill,
so install both.

## Use

**Building** — ask Claude Code to build a web app or AI product; the `production-web`
skill triggers and walks the enforced workflow, ending with a self-audit.

**Auditing** — point the subagent at existing code:

> Use the production-auditor to audit this repo for production readiness.

It classifies the product, grades every applicable checklist item, and returns a
scored table (Concern | Status | Evidence | Severity | Suggested fix). It is
**read-only** — it proposes fixes but applies them only when you explicitly ask.

## Test it

See [`examples/README.md`](examples/README.md) for a concrete scenario you can run
to confirm the auditor produces a useful report.

## Design

See [`DESIGN.md`](DESIGN.md) for the full architecture, the enforced workflow, the
AI testing pyramid, and the dual-purpose checklist format.

## Status

First-pass content — usable now; the checklists are broad but each still needs
deeper decision templates and worked eval examples. See the roadmap in `DESIGN.md`.

## License

MIT — see [`LICENSE`](LICENSE).
