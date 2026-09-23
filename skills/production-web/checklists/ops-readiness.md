# Ops readiness (light — expand later)

Dual-purpose.

---

## Secrets & config
- **Required (build):** secrets from env/secret-store, never committed; config
  per-environment; fail fast on missing required config at startup.
- **Audit signals:** no secrets in repo; env validation at boot.
- **Failure modes / Severity:** hard-coded secrets (**High**).

## Logging & monitoring
- **Required (build):** structured logs, correlation ids, error tracking; key
  health/metrics exposed.
- **Audit signals:** structured logging + error tracking wired.
- **Failure modes / Severity:** no error visibility in prod (**Med/High**).

## CI
- **Required (build):** tests + lint run on every change; build must pass to merge.
- **Audit signals:** CI config running the test suites.
- **Failure modes / Severity:** no CI gate (**Med**).

## Deployment readiness
- **Required (build):** health/readiness checks; graceful shutdown; migrations
  runnable and reversible.
- **Audit signals:** health endpoint; migration scripts.
- **Failure modes / Severity:** no health check / non-reversible migrations (**Med**).
