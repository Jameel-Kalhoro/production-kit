# Testing standard — all products

Dual-purpose. For AI products this is **in addition to** `testing-ai.md`.

---

## Unit tests
- **Required (build):** pure logic and each module tested in isolation; happy path
  AND error/edge paths; boundaries (empty, null, max, off-by-one).
- **Audit signals:** unit test files alongside modules; error paths asserted.
- **Failure modes / Severity:** only happy-path coverage (**Med**); none (**High**).

## Integration tests
- **Required (build):** real interactions between modules and with DB/external
  services (mocked or containerized); transaction/rollback behavior.
- **Audit signals:** integration suite hitting real boundaries.
- **Failure modes / Severity:** no integration coverage of critical paths (**High**).

## End-to-end tests
- **Required (build):** critical user journeys exercised through the real stack.
- **Audit signals:** e2e suite for primary flows.
- **Failure modes / Severity:** no e2e for the core journey (**Med/High**).

## Edge-case enumeration
- **Required (build):** for each feature, explicitly enumerate edge cases before
  coding tests (empty input, huge input, concurrent access, network failure,
  malformed data, auth failure).
- **Audit signals:** tests named after edge cases, not just "works".
- **Failure modes / Severity:** edge cases undocumented/untested (**Med**).

## Regression corpus
- **Required (build):** every fixed bug gets a test that fails before / passes after.
- **Audit signals:** a growing regression suite; tests referencing issue ids.
- **Failure modes / Severity:** fixed bugs recur (**Med**).

## Coverage expectation
- **Required (build):** state a coverage target and what's intentionally excluded;
  coverage is a signal, not a goal — prioritize critical-path and error-path lines.
- **Audit signals:** coverage reported; critical paths covered.
- **Failure modes / Severity:** critical paths uncovered regardless of % (**High**).
