# Frontend quality

Dual-purpose: **Required (build)** drives the skill; **Audit signals / Severity**
drive the production-auditor. Framework-agnostic — adapt to React/Vue/Svelte/etc.

> Frontend **testing** (component, interaction, visual-regression, automated a11y)
> lives in `testing-standard.md` → "Frontend testing" — one source of truth.

---

## State coverage (loading / error / empty / success)
- **Principle:** users see meaningful UI in every async outcome — never a blank or
  frozen screen.
- **Required (build):**
  - Every data-fetching view explicitly renders all four states: **loading**
    (skeleton/spinner), **success**, **empty** (no results — distinct from error),
    and **error** (message + retry).
  - Distinguish first load from background refetch — don't hide existing content
    behind a full spinner on refetch.
  - Loading placeholders reserve space (skeletons sized to content) so there's no
    layout shift when data arrives.
  - A request that never resolves eventually shows an error (timeout), not an
    infinite spinner.
  - Handle partial/streaming states where data arrives incrementally.
- **Audit signals:** components branch on loading/error/empty (not just render
  `data`); empty-state components exist; error states include a **retry** control;
  skeletons or fixed-size placeholders present.
- **Failure modes / Severity:** blank screen on error/empty (**High**); infinite
  spinner on a failed request (**High**); layout jump on load (**Med**); full spinner
  over content on every refetch (**Low/Med**).

## Form validation
- **Principle:** forms guide the user, never lose input, and never trust the client
  as the security boundary.
- **Required (build):**
  - Inline, **field-level** messages tied to the input (e.g. `aria-describedby`), not
    only a global alert.
  - **Server-side validation mirrors client rules** — client validation is UX only.
  - Submit disabled / shows spinner while in flight; **double-submit prevented**.
  - Validate on blur/submit (not aggressively per keystroke for slow rules); show
    success and error clearly.
  - **Preserve user input** on a failed submit; move focus to the first invalid field.
  - Correct input semantics (`type=email`, `inputmode`, `autocomplete`) for usability
    and mobile keyboards.
  - Warn on navigating away with **unsaved changes** where data loss matters.
- **Audit signals:** matching validation client + server; inputs have accessible error
  associations; submit handler guards re-entry with a disabled/loading button; fields
  keep values after a rejected submit.
- **Failure modes / Severity:** client-only validation (**High**); input lost on error
  (**Med/High**); no double-submit guard (**Med**); errors not associated with fields
  for screen readers (**Med**).

## Accessibility (a11y)
- **Principle:** usable by keyboard, screen reader, and low-vision users — not just
  mouse + sighted.
- **Required (build):**
  - **Semantic HTML first** (`button`, `nav`, `main`, ordered headings); ARIA only to
    fill genuine gaps.
  - Every interactive element **keyboard-reachable and operable**, with a visible
    focus indicator, logical tab order, and **no keyboard traps**.
  - Labels for all form controls; icon-only buttons have accessible names.
  - **Focus management**: on route change and modal open/close focus moves in, is
    trapped in the modal, and returns to the trigger on close.
  - Color **contrast ≥ WCAG AA** (4.5:1 text); information never conveyed by color
    alone.
  - `alt` on images (empty `alt` for decorative); `aria-live` regions announce async
    status/errors.
  - Respect `prefers-reduced-motion` for animations.
- **Audit signals:** labelled controls, `alt` attributes, heading hierarchy;
  `:focus-visible` styles; modal focus trap + restore; skip-to-content link; an
  automated a11y check (e.g. axe) in tests.
- **Failure modes / Severity:** unlabelled controls / icon buttons (**High**);
  keyboard trap or unreachable control (**High**); focus lost on route/modal change
  (**Med/High**); contrast failures (**Med**).

## Responsive design
- **Principle:** works from small mobile to large desktop and adapts to input type and
  viewport.
- **Required (build):**
  - Fluid/relative units with defined breakpoints; **no horizontal scroll** at mobile
    widths (test ~320–360px).
  - **Touch targets ≥ ~44px**; no hover-only interactions (must have tap/focus
    equivalent).
  - Responsive media (`max-width:100%`, `srcset`/`sizes`); handle overflow (tables,
    wide content) on small screens.
  - Tested at key breakpoints incl. very small and very large; handle safe-area/notch
    and orientation change.
  - Content reflows — no fixed pixel widths that clip.
- **Audit signals:** responsive CSS (media/container queries, flex/grid); `srcset` on
  images; no hover-only affordances; minimum touch-target sizing.
- **Failure modes / Severity:** horizontal scroll / clipped content on mobile
  (**Med/High**); hover-only controls unusable on touch (**Med**); tiny tap targets
  (**Med**).

## Error boundaries & resilience
- **Principle:** a single failing component or request degrades gracefully — it never
  takes down the whole app.
- **Required (build):**
  - **Error boundaries** around independent regions (routes, widgets) so one crash is
    contained and shows fallback UI.
  - Failed fetches show an error state with **retry**; transient failures retried with
    backoff where safe.
  - Global handlers for **uncaught errors and unhandled promise rejections**, reported
    to error tracking.
  - Fallback UI is useful (what failed + how to recover), never a raw stack trace to
    the user.
  - Guard against unexpected data shape (optional chaining/defaults) so a missing field
    doesn't crash render.
- **Audit signals:** error-boundary components at region/route level; retry paths on
  fetch errors; client error tracking wired (e.g. Sentry); a global rejection handler.
- **Failure modes / Severity:** one component error blanks the whole app (**High**);
  unhandled rejection with no reporting (**Med/High**); crash on an unexpected null
  field (**Med**).

## Performance basics
- **Principle:** fast initial load and smooth interaction — measured, not guessed.
- **Required (build):**
  - **Route-based code splitting / lazy loading**; a bundle-size budget with a check.
  - Avoid unnecessary re-renders (memoization, stable keys/callbacks) and **N+1 client
    fetches** (batch/cache).
  - **Virtualize** large lists; paginate or infinite-scroll instead of rendering
    thousands of nodes.
  - Optimize images (right format/size, lazy loading, **explicit dimensions to prevent
    CLS**).
  - Debounce/throttle expensive handlers (scroll, resize, search-as-you-type).
  - Defer non-critical work (idle/after interaction); avoid blocking the main thread.
- **Audit signals:** dynamic imports / lazy routes; a bundle analyzer or size check;
  memoization where heavy lists/trees exist; virtualized long lists; images lazy +
  dimensioned.
- **Failure modes / Severity:** huge single bundle / no code-splitting (**Med**);
  unbounded list rendering (**Med/High**); layout shift from undimensioned images
  (**Med**).

## Security (frontend)
- **Principle:** the browser is hostile territory — never trust it, and never let it
  execute or leak what it shouldn't.
- **Required (build):**
  - **No untrusted HTML injection**: avoid `dangerouslySetInnerHTML` / `v-html` /
    `innerHTML` with untrusted data; if unavoidable, sanitize (e.g. DOMPurify).
  - Rely on framework **output encoding** by default; treat all user/API data as
    untrusted.
  - Set a **Content Security Policy**; avoid inline scripts/`eval`; use nonce/hash if
    inline is required.
  - External links with `target="_blank"` use **`rel="noopener noreferrer"`** (reverse
    tabnabbing).
  - **Auth tokens**: prefer `httpOnly` cookies for session tokens; avoid `localStorage`
    for sensitive tokens (XSS-exfiltratable); add CSRF protection for cookie auth.
  - **No secrets in the bundle** — only publishable keys; verify what env vars are
    exposed to the client.
  - Clickjacking protection (`frame-ancestors`/`X-Frame-Options`); validate
    `postMessage` origins; sandbox iframes.
  - Dependency hygiene: audit for known-vuln packages; SRI on third-party scripts.
- **Audit signals:** no `dangerouslySetInnerHTML`/`v-html` on untrusted data (or
  sanitized); CSP present; `rel=noopener` on `_blank`; tokens not in `localStorage`;
  no API secrets in the bundle; `postMessage` origin checks.
- **Failure modes / Severity:** untrusted HTML injected → XSS (**High**); sensitive
  token in `localStorage` (**High**); secret shipped in bundle (**High**); no CSP
  (**Med**); missing `rel=noopener` (**Low/Med**).

## Data fetching & client state
- **Principle:** async data is correct, current, and consistent — no races, leaks, or
  stale UI. (Pairs with **State coverage** above.)
- **Required (build):**
  - **Race-condition safe**: ignore/abort out-of-order responses (request keys /
    `AbortController`) so the latest query wins.
  - **Cancellation** on unmount / param change — no `setState` after unmount, no wasted
    requests.
  - **Explicit caching**: define staleness and **invalidate on mutation** — neither
    refetch-everything nor stale-forever.
  - Deduplicate concurrent identical requests.
  - **Optimistic updates roll back** on failure; mutations invalidate/refresh affected
    queries.
  - Pagination/infinite-scroll handles concurrent loads, dedupes, and recovers from a
    mid-scroll error.
  - Deliberate boundary between **server state and UI state**; don't hand-roll caching
    a library already does correctly.
- **Audit signals:** `AbortController`/cancellation in effects; a data-fetching layer
  or explicit cache logic; stale-response guards; optimistic updates with rollback;
  query invalidation after mutations.
- **Failure modes / Severity:** race condition shows wrong data (**High**); no
  cancellation → leaks / setState-after-unmount (**Med**); stale data after mutation
  (**Med/High**); optimistic update with no rollback (**Med**).

## Core Web Vitals & loading performance
- **Principle:** measurable load and interaction performance against real thresholds,
  monitored in the field — not just a fast dev machine.
- **Required (build):**
  - State budgets against **good thresholds**: LCP < 2.5s, CLS < 0.1, INP < 200ms.
  - **CLS**: explicit `width`/`height` or `aspect-ratio` on images/media/embeds;
    reserve space for async content; preload fonts + `font-display: swap/optional`.
  - **LCP**: optimize the largest element (hero image `priority`/preload, critical CSS,
    SSR/streaming for fast first paint); serve via CDN.
  - **INP**: keep the main thread free — break up long tasks, defer/idle non-critical
    JS, minimize hydration cost.
  - **Bundle budget enforced in CI**; tree-shake, code-split, compress (gzip/brotli).
  - **Images**: modern formats (AVIF/WebP), responsive `srcset`, lazy-load below the
    fold, priority above the fold.
  - **Measure in the field** (RUM / `web-vitals`), not only lab; track regressions.
- **Audit signals:** dimensioned/aspect-ratio media; font preload + `font-display`;
  bundle-size + Lighthouse check in CI; priority hint on the LCP image; a web-vitals/
  RUM hook.
- **Failure modes / Severity:** CLS from undimensioned media/fonts (**Med/High**);
  slow LCP, no image priority/preload (**Med**); heavy hydration blocking INP
  (**Med**); no field measurement (**Low/Med**).

## Routing & auth UX
- **Principle:** navigation and session handling are predictable, secure, and never
  strand the user.
- **Required (build):**
  - A **404 / not-found** catch-all and a route-level error boundary.
  - **Protected routes** enforced client-side for UX **and** on the API server-side —
    the client guard is never the security boundary.
  - **Redirect-after-login**: bounce unauthenticated users to login, then return them
    to the originally requested URL.
  - **Deep-linking**: every meaningful view is URL-addressable and **reload-safe**
    (state reconstructable from the URL, not lost on refresh).
  - **Scroll restoration** on back/forward; move focus to main/heading on route change
    (a11y link).
  - **Session expiry**: on `401`, refresh silently or redirect to login **without
    losing unsaved work**; clear session everywhere on logout.
  - **Token refresh flow**: refresh at/near expiry, single-flight the refresh, queue
    in-flight requests during it.
  - Loading state during route transitions / lazy chunks; handle **chunk-load failure**
    (stale deploy) with a reload prompt.
- **Audit signals:** not-found + error routes; auth guards on protected routes plus
  server-side checks; return-URL after login; a `401` interceptor with refresh/
  redirect; scroll-restoration config; chunk-load-error handling.
- **Failure modes / Severity:** client-only route guard over an unprotected API
  (**High**); expired session → cryptic error or redirect loop (**High**); refresh
  loses unsaved work (**Med/High**); lost scroll/focus on nav (**Med**); no 404 route
  (**Low/Med**).
