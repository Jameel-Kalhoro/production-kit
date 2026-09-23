# Security Policy

## Scope

production-kit is a standard made of Markdown (a Claude Code skill, a subagent, and
checklists) — it ships no runtime service. The realistic security concerns are:

- **Prompt-injection or unsafe guidance** — an instruction in the skill/auditor that
  could lead a model to take an unsafe action, leak secrets, or produce insecure
  "standard" advice.
- **Any example scripts** added later under `examples/`.

## Reporting a vulnerability

Please report privately — **do not open a public issue** for a security problem.

- Email **jamal.tpapps@gmail.com** with a description, affected file(s), and steps to
  reproduce or the risky guidance in question.
- You'll get an acknowledgement within a few days.
- Once addressed, we'll credit you in the fix unless you prefer to stay anonymous.

## Supported versions

This is an early-stage standard; only the latest `main` is supported. Fixes land on
`main`.
