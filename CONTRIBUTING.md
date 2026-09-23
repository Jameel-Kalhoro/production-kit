# Contributing to production-kit

Thanks for helping make AI-assisted development more rigorous. This repo is a
**standard**, not an app — most contributions are edits to the shared checklists,
the skill, or the auditor. Quality of judgment matters more than volume.

## Ground rules

- **Stack-agnostic.** Every principle must adapt to any stack. Don't hard-code a
  framework or library as "the" way; describe the concern and the required outcome.
- **Concrete, not vague.** "Handle errors" is not a standard. "Map errors to status
  codes, never leak internals, test the error path" is.
- **Dual-purpose or it doesn't merge.** See the format below.

## The dual-purpose checklist format (required)

Every checklist item must serve *both* the build skill and the audit subagent:

```markdown
## Concern name
- **Principle:** one line — why this matters.
- **Required (build):** what must exist for this to be "done". Drives the skill.
- **Audit signals:** what to grep/look for to confirm it. Drives the auditor.
- **Failure modes / Severity:** what missing/broken looks like + High/Med/Low.
```

A PR that adds a "Required (build)" without matching "Audit signals" (or vice versa)
will be asked to complete the pair — otherwise building and grading drift apart.

## How to propose a change

1. **New concern or level?** Open an issue first (use the "Checklist addition"
   template) so we can agree it belongs before you write it.
2. **Fix / clarify existing content?** A PR directly is fine.
3. Keep PRs focused — one concern or one checklist per PR where possible.

## Testing your change

Install locally and exercise it:

```bash
cp -R skills/production-web ~/.claude/skills/
cp agents/production-auditor.md ~/.claude/agents/
```

- For skill changes: ask Claude Code to build something and confirm the workflow
  still gates correctly and the self-audit reflects your item.
- For checklist/auditor changes: run the `production-auditor` against a small repo
  and confirm your new **Audit signals** actually surface the gap with evidence.

## PR checklist

- [ ] Change is stack-agnostic and concrete.
- [ ] Any checklist item has all four parts (Principle / Required / Audit signals /
      Severity).
- [ ] No absolute machine paths (`grep -rn "/Users/" .` is clean; use `~/.claude`
      or relative paths).
- [ ] `DESIGN.md` roadmap/status updated if the change affects scope.

## License

By contributing, you agree your contributions are licensed under the repo's
[MIT License](LICENSE).
