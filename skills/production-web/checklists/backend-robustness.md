# Backend robustness

Dual-purpose: **Required (build)** drives the skill; **Audit signals / Severity**
drive the production-auditor. Stack-agnostic — adapt to the project's stack.

---

## Throttling / rate limiting
- **Principle:** protect the service and downstreams from overload and abuse.
- **Required (build):** an explicit decision — algorithm (token-bucket / sliding
  window / fixed window), scope (per-user / per-IP / per-key), limit + window,
  response (`429` + `Retry-After`), and backpressure on any internal queue.
- **Audit signals:** rate-limit middleware/layer present; 429 path tested;
  limits configurable, not hard-coded silently.
- **Failure modes / Severity:** no limiting at all (**High**); limiting without
  `Retry-After` (**Med**); global-only when per-user is needed (**Med**).

## Input validation
- **Required (build):** validate every external input at the boundary against a
  schema; reject early with a typed error; never trust client data.
- **Audit signals:** schema validation on every handler; tests for rejected input.
- **Failure modes / Severity:** unvalidated handlers (**High**); partial coverage
  (**Med**).

## Idempotency
- **Required (build):** unsafe operations (payments, writes triggered by retries)
  accept an idempotency key or are naturally idempotent.
- **Audit signals:** idempotency keys on mutating endpoints; dedupe logic tested.
- **Failure modes / Severity:** retries double-charge / double-write (**High**).

## Error handling
- **Required (build):** no unhandled rejections; errors mapped to correct status
  codes; internal detail not leaked; structured error responses.
- **Audit signals:** central error handler; error-path tests; no raw stack traces
  to clients.
- **Failure modes / Severity:** leaks internals / 500s on expected errors (**Med/High**).

## Connection pooling & resource limits
- **Required (build):** DB/HTTP clients pooled with bounded size; timeouts on every
  external call; connections released on error.
- **Audit signals:** pool config present; timeouts set; no per-request new client.
- **Failure modes / Severity:** unbounded connections / no timeouts (**High**).

## Caching
- **Required (build):** explicit decision on what is cached, TTL, invalidation, and
  stampede protection where relevant.
- **Audit signals:** cache layer with TTL + invalidation path.
- **Failure modes / Severity:** stale-forever cache / no invalidation (**Med**).

## Pagination & bounded responses
- **Required (build):** list endpoints paginated; no unbounded result sets.
- **Audit signals:** limit/offset or cursor on collections; max page size enforced.
- **Failure modes / Severity:** unbounded list endpoints (**Med/High**).

## Auth & authorization edge cases
- **Required (build):** authN and authZ on every protected route; object-level
  authorization (user can only access their own resources); token expiry/refresh.
- **Audit signals:** authz checks per route; tests for cross-user access denial.
- **Failure modes / Severity:** missing object-level authz / IDOR (**High**).

## Observability
- **Required (build):** structured logs with correlation ids; key metrics; no secrets
  in logs.
- **Audit signals:** structured logging; correlation id propagation.
- **Failure modes / Severity:** unstructured/absent logging (**Med**).
