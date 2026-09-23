# Rate limiter — decision template

Fill this in explicitly for the project; do not accept silent defaults.

| Decision | Options | Choose |
|---|---|---|
| Algorithm | token-bucket (bursty-friendly) / sliding window (accurate) / fixed window (cheap, edge bursts) | |
| Scope | per-user / per-IP / per-API-key / per-route / global | |
| Limit + window | e.g. 100 req / 60s; burst 20 | |
| Storage | in-memory (single node) / Redis (multi-node, atomic) | |
| Over-limit response | `429` + `Retry-After` + rate-limit headers (`X-RateLimit-*`) | |
| Internal backpressure | queue bound + shed vs block when downstream saturated | |
| Exemptions | health checks, internal service-to-service | |

Notes:
- Multi-node ⇒ shared store (Redis) with atomic increment, else limits are per-node.
- Always return `Retry-After`; clients need it to back off correctly.
- Rate limiting is not abuse protection alone — pair with auth and quota where needed.
