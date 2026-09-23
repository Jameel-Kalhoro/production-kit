# Frontend quality

Dual-purpose. Adapt to the project's framework.

---

## State coverage (loading / error / empty / success)
- **Required (build):** every async view renders all four states, not just success.
- **Audit signals:** components handle loading + error + empty explicitly.
- **Failure modes / Severity:** blank screen on error/empty (**Med/High**).

## Form validation
- **Required (build):** client-side validation with clear messages, mirrored by
  server-side validation (never client-only); disabled/spinner on submit; no double
  submit.
- **Audit signals:** validation on inputs + matching server validation.
- **Failure modes / Severity:** client-only validation (**High**); no submit guard (**Med**).

## Accessibility (a11y)
- **Required (build):** semantic HTML; labels for inputs; keyboard navigable; focus
  management; adequate contrast; ARIA only where needed.
- **Audit signals:** labelled controls; keyboard/focus handling; alt text.
- **Failure modes / Severity:** unlabelled controls / keyboard traps (**Med/High**).

## Responsive design
- **Required (build):** works across target breakpoints; no fixed widths that break
  mobile; touch targets adequate.
- **Audit signals:** responsive layout; tested at small viewport.
- **Failure modes / Severity:** broken mobile layout (**Med**).

## Error boundaries & resilience
- **Required (build):** UI-level error boundaries so one failing component doesn't
  blank the app; retry affordances on failed fetches.
- **Audit signals:** error boundary present; retry paths.
- **Failure modes / Severity:** whole-app crash on one component (**Med/High**).

## Performance basics
- **Required (build):** avoid unnecessary re-renders / N+1 fetches; lazy-load heavy
  routes; bounded lists virtualized where large.
- **Audit signals:** code-splitting; memoization where it matters.
- **Failure modes / Severity:** heavy unbounded renders (**Med**).
